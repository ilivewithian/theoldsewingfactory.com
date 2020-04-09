---
layout: post
title: Using jQuery UI Autocomplete with WCF services
date: 2010-11-03
---

Demo: [http://autocomplete.theoldsewingfactory.com/](http://autocomplete.theoldsewingfactory.com/)

Source: [https://github.com/ilivewithian/AutocompleteWCF](https://github.com/ilivewithian/AutocompleteWCF)

I'm making the following basic assumptions about the reader, firstly that you are familiar with jQuery and jQuery UI. I'm also assuming that you have at least a basic grasp of WCF services.

## Building the WCF Service

To your project add an `AJAX-enabled WCF Service`, this gives you the bare bones of what we need service side. Since our auto complete control defaults to using `HTTP GET` rather than `HTTP POST`, lets get our service to play nice. It's very simple, just add the attribute `[WebGet]` to the method we want to call. Also, I prefer working with JSON when passing data around the web, so I added the parameter `ResponseFormat = WebMessageFormat.Json` to the attribute. The default method that our service comes with returns void, which is clearly not helpful for our scenario. Replace that with the type of data you want to return (it will get automagically serialized into JSON for us, so you don't have to worry too much about the type of data). For out example I'm returning an array of a simple class `WordResult`, which is defined like this:

    public class WordResult
    {
        public string Word { get; set; }
        public int Length { get; set; }
        public string Reversed { get; set; }
    }

From jQuery I want to pass the text that the user has typed and the maximum number of results to return, so I'll add a couple of additional parameters to my method and I don't like the name of the method, so I'll rename it. I now have a class that looks a bit like this:

    [ServiceContract(Namespace = "autocomplete.theoldsewingfactory.com")]
    [AspNetCompatibilityRequirements(RequirementsMode = AspNetCompatibilityRequirementsMode.Allowed)]
    public class AutoComplete
    {
        [OperationContract]
        [WebGet(ResponseFormat = WebMessageFormat.Json)]
        public WordResult[] WordLookup(string text, int count)
        {
            return new WordResult[] {
                new WordResult
                {
                    Word = text,
                    Length = text.Length,
                    Reversed = Reverse(text)
                }
            };
        }
    
        public static string Reverse(string s)
        {
            char[] charArray = s.ToCharArray();
            Array.Reverse(charArray);
            return new string(charArray);
        }
    }

Obviously the search results in my example are somewhat dull, but if you take a look at the working example and the completed code and you'll see something slightly more useful.

We now have a functioning WCF service that will place nicely with jQuery, so onto the web page.

## Hooking jQuery into our WCF Service

This part is well documented on the jQuery UI site, so I won't go into depth other than to say that the URL you use to call the service is of the form <Full Service URL>/<MethodName> and that the data parameters are the names of your WCF service methods parameters. So in our case the call to configure the autocomplete is:


    $("input[id$='targetTextbox']").autocomplete({
        source: function (request, response) {
            $.ajax({
                url: "AutoComplete.svc/WordLookup",
                data: {
                    text: request.term,
                    count: 15
                },
                dataType: "json",
                contentType: "application/json; charset=utf-8",
                success: function (data) {
                    response($.map(data.d, function (item) {
                        return {
                            value: item.Word,
                            length: item.Length,
                            otherWayAround: item.Reversed
                        }
                    }))
                },
                error: function (XMLHttpRequest, textStatus, errorThrown) {
                    $("#result").text(textStatus);
                }
            });
        },
        minLength: 2,
        select: function (event, ui) {
            $("#result").text("Selected: " + ui.item.value + " with " + ui.item.length + "characters, and just for fun (of a sort) backwards: " + ui.item.otherWayAround);
        }
    });

So, go and play with the demo app and take a look at the source, you should find everything you need.
