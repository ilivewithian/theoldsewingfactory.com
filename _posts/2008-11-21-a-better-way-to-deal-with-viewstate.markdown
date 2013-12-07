---
layout: post
title: A Better way to Deal with Viewstate
---

I'm going to assume that by the point you are reading this article you already know what viewstate is, why it will cause you problems and what you should have already done about it.

If you haven't then start by reading [this](http://weblogs.asp.net/infinitiesloop/archive/2006/08/03/Truly-Understanding-Viewstate.aspx) post.

## Turn ViewState off

The best thing you can do is to turn ViewState off. You can do this by turning it off on the whole site by editing your web.config file:

    <pages enableViewState="false" />

You can turn it off on a page:

    <%@ Page Language="C#" EnableViewState="false" %>

Or you can disable on individual controls:

    <asp:GridView EnableViewState="false"></ asp:GridView >

On the face of it that's the problem solved, wrong, dead wrong. Some controls will ignore the fact that ViewState is disabled and still use it any way. The GridView is one of the more notable examples. Also, you may find that this is not practical. You may need to use ViewState, you may have third party controls that require ViewState. Or simply you may have inherited a large site that makes use of ViewState extensively and removing it would take too much time.

## Get Rid of ViewState Completely


Ok, so luckily the boys who wrote asp .net had their heads screwed on, in the Page object we are given two methods that we can override:

    protected override void SavePageStateToPersistenceMedium(object state);
    protected override object LoadPageStateFromPersistenceMedium();

Each and every page cycle these two methods are called. All we need to do is override the default behavior and do something else.

The following code gives us two alternatives to the default behavior, we can either move the ViewState into the session object, or if that’s no good we can compress the ViewState and send a smaller packet instead.

To use it, simply change your page to inherit from `TheOldSewingFactory.ViewstateSample.BasePage` rather than the standard `System.Web.UI.Page`.

Then on your page you can set the property ViewStateMode or you can add a new appSetting:

    <appSettings>
        <!--This setting can be "session", "compress" or "default" -->
		<add key="ViewStateMode" value="compress"/>
	</appSettings>

Let me know what you think.

    using System;
    using System.Configuration;
    using System.ComponentModel;
    using System.IO;
    using System.IO.Compression;
    using System.Runtime.Serialization.Formatters.Binary;
    using System.Text;
    using System.Web.SessionState;
    using System.Web.UI;
     
    
    namespace TheOldSewingFactory.ViewstateSample
    {
        public enum ViewStateConfig
        {
            Session,
            Compress,
            Default,
            NotSet
        }
    
        public partial class BasePage : System.Web.UI.Page
        {
            private const string ViewStateModeSetting = "ViewStateMode";
            private const string ViewStateSessionId = "ViewStateSessionId";
            private const string CompressedViewStateId = "CompressedViewState";
    
            private ViewStateConfig _viewStateMode = ViewStateConfig.NotSet;
            [Browsable(true)]
            [Category("Behaviour")]
            [DefaultValue(ViewStateConfig.NotSet)]
            public ViewStateConfig ViewStateMode
            {
                get
                {
    
                    //The setting on the page overrdes the one on the site config.
                    if (_viewStateMode != ViewStateConfig.NotSet)
                    {
                        return _viewStateMode;
                    }
                    else if (!string.IsNullOrEmpty(ConfigurationManager.AppSettings[ViewStateModeSetting]))
                    {
                        return (ViewStateConfig)Enum.Parse(typeof(ViewStateConfig), ConfigurationManager.AppSettings[ViewStateModeSetting], true);
                    }
                    else
                    {
                        return ViewStateConfig.Default;
                    }
                }
                set
                {
                    _viewStateMode = value;
                }
            }
    
            protected override void SavePageStateToPersistenceMedium(object state)
            {
                switch (ViewStateMode)
                {
                    case ViewStateConfig.Session:
                        //Since the session isn't always availible, we had better make sure we
                        //do something sensible
                        if (this.Session.Mode != SessionStateMode.Off)
                        {
                            this.SaveToSession(state);
                        }
                        else
                        {
                            this.CompressViewstate(state);
                        }
                        break;
                    case ViewStateConfig.Compress:
                        this.CompressViewstate(state);
                        break;
                    default:
                        base.SavePageStateToPersistenceMedium(state);
                        break;
                }
            }
    
            protected override object LoadPageStateFromPersistenceMedium()
            {
                switch (ViewStateMode)
                {
                    case ViewStateConfig.Session:
                        if (this.Session.Mode != SessionStateMode.Off)
                        {
                            return this.LoadFromSession();
                        }
                        else
                        {
                            return this.DecompressViewstate();
                        }
                    case ViewStateConfig.Compress:
                        return this.DecompressViewstate();
                    default:
                        return base.LoadPageStateFromPersistenceMedium();
                }
            }
    
            private void SaveToSession(object state)
            {
                //First lets see if there is already a session id availible to us
                string viewStateSessionId = base.Request.Form[ViewStateSessionId];
                if(string.IsNullOrEmpty(viewStateSessionId))
                {
                    //If there isn't then we'll be needing one.
                    viewStateSessionId = Guid.NewGuid().ToString();
                }
    
                //Save the data into our session object
                Session[viewStateSessionId] = state;
    
                //Lastly we save the sessionid for when the page is loaded.
                ScriptManager.RegisterHiddenField(this, ViewStateSessionId, viewStateSessionId);
            }
    
            private object LoadFromSession()
            {
                return Session[base.Request.Form[ViewStateSessionId]];
            }
    
            private void CompressViewstate(object state)
            {
                //The ObjectStateFormatter is explicitly for serializing
                //viewstate, if you're using .net 1.1 then use the LosFormatter
    
                //First off, lest gets the state in a byte[]
                ObjectStateFormatter formatter = new ObjectStateFormatter();
                byte[] bytes;
                using (MemoryStream writer = new MemoryStream())
                {
                    formatter.Serialize(writer, state);
                    bytes = writer.ToArray();
                }
    
                //Now we've got the raw data, lets squish the whole thing
                using (MemoryStream output = new MemoryStream())
                {
                    using (DeflateStream compressStream = new DeflateStream(output, CompressionMode.Compress, true))
                    {
                        compressStream.Write(bytes, 0, bytes.Length);
                    }
    
                    //OK, now lets store the compressed data in a hidden field.
                    ScriptManager.RegisterHiddenField(this, CompressedViewStateId, Convert.ToBase64String(output.ToArray()));
                }
            }
    
            private object DecompressViewstate()
            {
                //First lets get ths raw compressed string into a byte[]
                byte[] bytes = Convert.FromBase64String(Request.Form[CompressedViewStateId]);
    
                using (MemoryStream input = new MemoryStream(bytes))
                {
                    //Now push the compressed data into the decompression stream
                    using (DeflateStream decompressStream = new DeflateStream(input, CompressionMode.Decompress, true))
                    {
                        using (MemoryStream output = new MemoryStream())
                        {
                            //Now we wip through the decompression stream and pull our data back out
                            byte[] buffer = new byte[256];
                            int data;
                            while ((data = decompressStream.Read(buffer, 0, buffer.Length)) > 0)
                            {
                                output.Write(buffer, 0, data);
                            }
    
                            //Finally we convert the whole lot back into a string and convert it
                            //back into it's original object.
                            ObjectStateFormatter formatter = new ObjectStateFormatter();
                            return formatter.Deserialize(Convert.ToBase64String(output.ToArray()));
                        }
                    }
                }
            }
        }
    }
