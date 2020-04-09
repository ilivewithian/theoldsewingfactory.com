---
layout: post
title: Simplifying Session Wrapping Properties using Expression&lt;Func&gt;
date: 2010-09-24
---

I'm not a big fan of using session, but sometimes there isn't a sensible alternative. In that case I _always_ wrap the session object in a property. In the past I've failed to come up with a satisfactory session variable naming convention. With that I've always hated using strings for session names. Each solution basically boils down to typing some strings some where in your code, either directly into the call to `Session[]` or the same process, but via a series of predefined const strings. Using a const string gets you slightly cleaner code, but there is nothing to prevent a developer picking the wrong const and ending up in a delightful mess.

Once you've got past the string mess, you then end up having to write code to check the session value, set a default value and return. Sure, it's not hard, but you will end up writing a lot of repetitive code. That's boring, laborious and error prone.

To try and fix both the string problem and to try and keep my code nice and D.R.Y I've come up with is this:

    public class SessionManager: ISessionManager
    {
        private HttpSessionState Session
        {
            get { return HttpContext.Current.Session; }
        }
    
        public T GetSessionValue(Expression<Func<T>> expression, T defaultValue)
        {
            var sessionKey = GetSessionKey(expression);
            object sessionValue = Session[sessionKey];
    
            if (sessionValue == null || ! typeof(T).IsAssignableFrom(sessionValue.GetType()))
            {
                Session[sessionKey] = defaultValue;
                sessionValue = defaultValue;
            }
    
            return (T)sessionValue;
        }
    
        public void SetSessionValue(Expression<Func<T>> expression, T value)
        {
            var sessionKey = GetSessionKey(expression);
    
            Session[sessionKey] = value;
        }
    
        private string GetSessionKey(Expression<Func<T>> expression)
        {
            MemberExpression me;
    
            var body = expression.Body;
    
            if (body is MemberExpression)
            {
                me = body as MemberExpression;
            }
            else if (body is UnaryExpression)
            {
                var ue = body as UnaryExpression;
                me = ue.Operand as MemberExpression;
            }
            else
            {
                throw new NotImplementedException();
            }
    
            return "SessionManager_" + me.Member.ReflectedType + "." + me.Member.Name;
        }
    }

To use this I would simple create a property similar to this:

    public IList AnImportantProperty
    {
        get
        {
            return SessionManager.GetSessionValue(() => AnImportantProperty, new List());
        }
        set
        {
            SessionManager.SetSessionValue(() => AnImportantProperty, value);
        }
    }

Now my session is tidily hidden away. It will survive refactoring of the property name and guarantee a unique session name. To my eyes it looks clean, simple and D.R.Y.
