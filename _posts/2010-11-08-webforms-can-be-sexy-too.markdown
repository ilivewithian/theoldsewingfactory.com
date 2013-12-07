---
layout: post
title: ASP .Net Webforms can be Sexy too
---

Source code: <a href="https://github.com/ilivewithian/SexyWebforms">https://github.com/ilivewithian/SexyWebforms</a>
For all the frustration that is felt as an ASP .Net Webforms developer, ASP .Net Webforms applications don't have to be ugly. They can produce nice, easy to work with, valid html. Today I'm going to cover one small way that can help make your sites a little bit nicer.
## Sexy CSS Buttons

Hopefully you have come across <a href="http://www.oscaralexander.com/tutorials/how-to-make-sexy-buttons-with-css.html">http://www.oscaralexander.com/tutorials/how-to-make-sexy-buttons-with-css.html</a>. It's not a new technique, but it is a really nice way of giving your users nice looking, variable width buttons. Essentially it's a case of embedding an additional `<span>` tag inside an `<a>` tag. You could always do this by hand on each and every LinkButton and HyperLink, but that's unnecessary hard work.
## Adapt the Controls

So assuming that you don't want to litter your server side mark-up and (shame on you) code behind with `<span>` tags, let's find a more elegant solution. The option I've gone for is to build two very simple control adapters, coded like this:
    public class LinkButtonAdapter : WebControlAdapter
    {
        protected override void RenderContents(System.Web.UI.HtmlTextWriter writer)
        {
            var linkButton = Control as LinkButton;
            linkButton.Text = string.Format("<span>{0}</span>", linkButton.Text);
            base.RenderContents(writer);
        }
    }

And
    public class HyperLinkAdapter : WebControlAdapter
    {
        protected override void RenderContents(System.Web.UI.HtmlTextWriter writer)
        {
            var link = Control as HyperLink;
            link.Text = string.Format("<span>{0}</span>", link.Text);
            base.RenderContents(writer);
        }
    }
Now, it's a simple case of registering these adapters in a browsers file:

    <browsers>
	    <browser refID="Default">
			<controlAdapters>
				<adapter controlType="System.Web.UI.WebControls.LinkButton"
						 adapterType="SexyAdapters.LinkButtonAdapter" />
				<adapter controlType="System.Web.UI.WebControls.HyperLink"
						 adapterType="SexyAdapters.HyperLinkAdapter" />
			 </controlAdapters>
		</browser>
	</browsers>
Simple! No need to hack around in a large amounts of our site, simply add the button CSS class to our controls and we have lovely looking buttons, without lots of hassle.