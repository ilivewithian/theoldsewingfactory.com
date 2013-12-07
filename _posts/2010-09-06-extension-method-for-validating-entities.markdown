---
layout: post
title: Extension method for validating entities.
---

I've written this a couple of times this morning:
    if (string.IsNullOrWhiteSpace(Name))
    {
        errors.Add(new KeyValuePair("Name", "Name must contain some text"));
    }
    
    if ((Name ?? "").Length > 50)
    {
        errors.Add(new KeyValuePair("Name", "Name cannot be more that 50 characters long"));
    }
I couldn't help but feel that if I had to write this more than about twice I would go nuts, never mind the obvious maintenance problems. If I rename a property I want the correct property name in the errors collection, not the name of the property from 9 months ago.
So since all this validation is being done at the domain level and all my domain entities implement the interface IBaseEntity (come on EF, let me pick my own base class)  I decided I'd write myself a little validation function that would use lambdas to take a property and validate its value meets the constraints. So I did and ended up with the following function:
    public static void ValidateRequiredFieldWithMaxLength(this IBaseEntity entity, Expression<Func<T>> e, int maxLength, IList<KeyValuePair<string, string>> errors)
    {
        if (errors == null)
        {
            throw new ArgumentNullException("errors");
        }
    	
        MemberExpression member = (MemberExpression)e.Body;
        Expression strExpr = member.Expression;    
        string propertyName = member.Member.Name;    
        string value = e.Compile()();    
        if (string.IsNullOrWhiteSpace(value))        {            errors.Add(new KeyValuePair(propertyName, string.Format("{0} must contain some text", propertyName)));        }    
        if ((value ?? "").Length > maxLength)        {            errors.Add(new KeyValuePair(propertyName, string.Format("{0} cannot be more than {1} characters long", propertyName, maxLength)));        }    }
Which may not be immediately obvious to you, but the long and the short is that I can now call my validation functions like this:
    this.ValidateRequiredFieldWithMaxLength(() => this.Code, 10, errors);
It's not perfect and I might end up fiddling around with it a bit more, but for now I like it.