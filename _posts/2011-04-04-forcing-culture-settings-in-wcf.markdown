---
layout: post
title: Forcing Culture Settings in WCF
---

I had the need to force the thread culture on a WCF service recently. The culture kept dropping back to en-US, but I needed it to run in en-GB as the client was sending me files with UK dates in them. After banging my head against every setting I could find I gave up and opted to write a custom WCF service and force the culture. It’s not a pretty hack, but it works.
If you use this and it breaks your servers, steals your wallet and then runs off with your wife, consider it your fault for using code from some random blog post you found. Caveat emptor applies.
You can download a working sample here: <a href='http://theoldsewingfactory.com/wp-content/uploads/2011/04/WCFCulture.zip'>WCFCulture</a>
First up we need to create a message inspector to hook in and mush about with our messages:    public class CultureMessageInspector : IDispatchMessageInspector
    {
    	private readonly string _culture;
    	public CultureMessageInspector(string culture)
    	{
    		_culture = culture;
    	}    
    	public object AfterReceiveRequest(ref Message request, IClientChannel channel, InstanceContext instanceContext)
    	{
    		Thread.CurrentThread.CurrentCulture = new CultureInfo(_culture);
    		return null;
    	}    
    	public void BeforeSendReply(ref Message reply, object correlationState)
    	{
    	}
    }
Then we need to hook this into an endpoint behaviour:
    public class CultureBehavior : IEndpointBehavior
    {
    	private readonly string _culture;
    	public CultureBehavior(string culture)
    	{
    		_culture = culture;
    	}    
    	public void AddBindingParameters(ServiceEndpoint endpoint, BindingParameterCollection bindingParameters)
    	{    
    	}    
    	public void ApplyClientBehavior(ServiceEndpoint endpoint, ClientRuntime clientRuntime)
    	{    
    	}    
    	public void ApplyDispatchBehavior(ServiceEndpoint endpoint, EndpointDispatcher endpointDispatcher)
    	{
    		var inspector = new CultureMessageInspector(_culture);
    		endpointDispatcher.DispatchRuntime.MessageInspectors.Add(inspector);
    	}    
    	public void Validate(ServiceEndpoint endpoint)
    	{    
    	}    
    }
And because we want to configure the culture we end up using, we need a custom extension element:
    public class CultureExtensionElement : BehaviorExtensionElement
    {
    	[ConfigurationProperty("Culture", DefaultValue = "en-GB")]
    	public string Culture
    	{
    		get { return (string)base["Culture"]; }
    		set { base["Culture"] = value; }
    	}    
    	protected override object CreateBehavior()
    	{
    		return new CultureBehavior(Culture);
    	}    
    	public override Type BehaviorType
    	{
    		get { return typeof(CultureBehavior); }
    	}
    }

Ok, so assuming that you can get that pretty lot to compile all we need to do is hook it in via the `web.config` in the services project.
First register the extension:
    <system.serviceModel>
    	<extensions>
    		<behaviorExtensions>
    			<add name="WCFCulture" type="WCFCulture.CultureExtensionElement, WCFCulture, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    		</behaviorExtensions>
    	</extensions>
    </system.serviceModel>
Then to your behaviour simply add:
    <WCFCulture Culture="en-GB" />

Your resulting config should look a bit like this:
    <system.serviceModel>
    	<behaviors>
    		<serviceBehaviors>
    			<behavior name="">
    				<serviceMetadata httpGetEnabled="true" />
    				<serviceDebug includeExceptionDetailInFaults="false" />
    			</behavior>
    		</serviceBehaviors>
    		<endpointBehaviors>
    			<behavior name="">
    				<WCFCulture Culture="en-US" />
    			</behavior>
    		</endpointBehaviors>
    	</behaviors>
    	<extensions>
    		<behaviorExtensions>
    			<add name="WCFCulture" type="WCFCulture.CultureExtensionElement, WCFCulture, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    		</behaviorExtensions>
    	</extensions>
    	<serviceHostingEnvironment multipleSiteBindingsEnabled="true" />
    </system.serviceModel>
So now, it won't matter what your client wants the culture to be, you can override it in config.