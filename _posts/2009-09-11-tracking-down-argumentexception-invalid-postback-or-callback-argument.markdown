---
layout: post
title: Tracking Down ArgumentException "Invalid postback or callback argument."
---

The longer you use asp.net webforms the more likely it is that you will see this:

    Exception type: System.ArgumentException
    Exception message: Invalid postback or callback argument. Event validation is enabled using <pages enableEventValidation="true"/> in configuration or <%@ Page EnableEventValidation="true" %> in a page.  For security purposes, this feature verifies that arguments to postback or callback events originate from the server control that originally rendered them.  If the data is valid and expected, use the ClientScriptManager.RegisterForEventValidation method in order to register the postback or callback data for validation.
If you're lucky enough to have it happen on a dev machine and you know which control is causing the problem you're most of the way to tracking down the error. Just read <a href="http://blogs.msdn.com/amitsh/archive/2007/07/31/why-i-get-invalid-postback-or-callback-argument-errors.aspx">this</a>, <a href="http://blogs.msdn.com/carloc/archive/2008/05/02/invalid-postback-or-callback-argument-in-asp-net.aspx">this</a> and <a href="http://niravbhattsai.blogspot.com/2007/10/invalid-postback-or-callback-argument.html">this</a>.

If like me, you can't track it down, all you're getting is vague error reports and users who can't remember what they did (or curiously didn't even see the error) then you need to know which control actually caused the error.

This is what I did:
    Imports System.Net.Mail    
	Namespace Code.Controls
	
	    Public Class TrackingLinkButton
            Inherits LinkButton
            Protected Overrides Sub RaisePostBackEvent(ByVal eventArgument As String)
                Try
                    MyBase.RaisePostBackEvent(eventArgument)
                Catch argExc As ArgumentException
                    SendPostbackError(argExc)
                End Try    
            
			End Sub        
            Private Sub SendPostbackError(ByVal argExc As ArgumentException)
        
                Dim subject As String = "ArgumentException: Additional info"
                Dim body As String = String.Format("Exception was caused by the control with ID: {0} and clientId of: {1}", _
                                                   Me.ID, Me.ClientID)
    			Dim msg As New MailMessage("from@somesite.com", "support@somesite,com", _    										subject, body)
    			Dim client As New SmtpClient()
    			client.Send(msg)
			
			End Sub          
    	End Class      
    End Namespace
And hooked it in with this:

    <tagMapping>
        <add tagType="System.Web.UI.WebControls.LinkButton"
              mappedTagType="AcademyPro.Code.Controls.TrackingLinkButton"/>    </tagMapping>
Now all I have to do it sit back and wait for the email to fire and I should be able to track down the error (I hope).