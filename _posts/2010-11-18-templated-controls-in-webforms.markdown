---
layout: post
title: Templated Controls in WebForms
---

Today I had needed to create a templated control, please don't think less of me. I wanted to avoid calling FindControl repeatedly to get the controls inside my template and I also needed it to work with update panels. I didn't expect it to be difficult, now I know how it's not a problem, but for some reason this proved to be a real pain at the office.
For those just interested in the source code, head over here: <a href="https://github.com/ilivewithian/Templated-Controls-in-Webforms">https://github.com/ilivewithian/Templated-Controls-in-Webforms</a>

Still here, I'll explain what I now know:
    public class TemplatedControl : WebControl
Very simply this is the name of our control and we make it a control that we can use by inheriting from WebControl. So switch TemplatedControl for MySuperSpecicalDialogBox or whatever works for you.
    public TemplatedControl()
       : base(HtmlTextWriterTag.Div)
    {    
    }

This is our default constructor, I want my control to wrap everything in a Div, so I make sure I call the base constructor with a div tag. If I don't do that it will default to a span, which may or may not be what you're after.
    TemplateInstance(TemplateInstance.Single)]    public ITemplate MyTemplate
This is the actual template, don't name it MyTemplate in your control, that would be silly. The name of the property is the name that you use in your markup, so in a real control you may name this something like Header or ContentTemplate (as in the case of the UpdatePanel).
The attribute is a hint to the designer and the runtime about how your control will be used. In my case I would only ever have a single instance of the template, doing so means that I am allowed to access the controls contained inside it by name, rather than my calling FindControl on each instance of the template. If I needed a repeating template, in the case of a list or table style control I would have to drop the TemplateInstance(TemplateInstance.Single) and replace it with TemplateInstance(TemplateInstance.Multiple).
Lastly I actually build up the template:
    set
    {
        this._myTemplate = value;
        if (this._myTemplate != null)
        {
            var container = new Control();
            base.Controls.Add(container);
            _myTemplate.InstantiateIn(container);
        }
    }
It's as obvious as it looks, create something to put our template into, add that to the parent controls collection and then tell the template that we've been given to instantiate itself into our target control. Simple.
Whilst I now no longer need this, I do plan on comparing this to the work that I have in the office and try to figure out why this very simple example works and the one I built was giving back null controls and breaking the way that update panels worked. All in all a frustrating day wasted at work and a much less frustrating 2 hours spent this evening.
Oh yes, finally here's how to use the control in your mark up:
    <OSF:TemplatedControl ID="TemplatedControl1" runat="server">
        <MyTemplate>
            Roll up, roll up, look at my exciting content, be amazed by this mind 
            boggling <asp:button text="text" runat="server" /> and this 
            even more amazing <asp:label text="text" runat="server" />
            wow, isn't webforms excting.
        </MyTemplate>
    </OSF:TemplatedControl>
I hope this saves someone a bunch of effort.