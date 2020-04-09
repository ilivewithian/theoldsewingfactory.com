---
layout: post
title: Arbitrary HTML in the AutoCompleteExtender
date: 2009-01-20
---

The auto complete extender comes as part of the AjaxControlToolkit, it's a really neat little extension to a TextBox that allows a user to be given suggestions based on what they type.

It is currently restricted so that it only shows text and no more. I’ve made a couple of changes that allow you to pass arbitrary html, which makes things a like more interesting.

Ok, so in the first thing I need to be able to do is pass the html from the webservice. This bit is easy, all we need to do is create an overload to AutoCompleteExtender. CreateAutoCompleteItem method. That’s easy, just add the following method to the AutoCompleteExtender class:

    public static string CreateAutoCompleteItem(string text, string value, string markup)
	{
	    return new JavaScriptSerializer().Serialize(new Pair(text, new Pair(value, markup)));
    }

Now we need to make sure that the html is displayed, this is done by the `_update` method in AutoCompleteBehavior.js. In this method there is a loop, this spins through each of the list items that comes back from the web service.  Essentially I detect if html is supplied and if it is then use the html, rather than simply putting a text node in place.

The other change is to the `_setText` function, this must search through the parent nodes to find which one has the text and id values which we sent from our web service.

The two files are attached, take them and drop them into your project, all your old code should continue to work, but you could write a web service like this to display html:


    Public Function GetPersonList(ByVal prefixText As String, ByVal count As Integer) As String()
        Dim table As DataSet
		table = Database.Fill("PersonSearch", New SqlParameter() { _
								Database.CreateParameter("@Name", SqlDbType.NVarChar, ParameterDirection.Input, prefixText), _
								Database.CreateParameter("@Count", SqlDbType.Int, ParameterDirection.Input, count)})

	    Dim list(table.Tables(0).Rows.Count - 1) As String
    	Dim html As String
    
    	For i As Integer = 0 To list.Length - 1
    		html = String.Format("{0} {1}", _
    								table.Tables(0).Rows(i)("Forename"), _
    								table.Tables(0).Rows(i)("Surname"))
    
    		list(i) = AutoCompleteExtender.CreateAutoCompleteItem(String.Format("{0} {1}", table.Tables(0).Rows(i)("Forename"), table.Tables(0).Rows(i)("Surname")), _
    											String.Format("{0}", table.Tables(0).Rows(i)("PersonId")), _
    											html)    
    	Next
    
    	Return list
    
    End Function


That's it, you now have a list of people, with a picture next their name, which is nice.

I hope this helps someone.


Here's the complete modified AutoCompleteBehavior.js: [http://www.mediafire.com/?4ztezjxnfnl](http://www.mediafire.com/?4ztezjxnfnl) and the modified AutoCompleteExtender.cs: [http://www.mediafire.com/?5idmqczandm](http://www.mediafire.com/?5idmqczandm)
