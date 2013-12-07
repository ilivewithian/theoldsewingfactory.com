---
layout: post
title: NeverNull - because we can't extend the ?? operator
---

This morning [@JeffHandley](http://twitter.com/JeffHandley) tweeted this [http://www.forkcan.com/viewcode/277/Null-Dot-Operator-Extension-Method](http://www.forkcan.com/viewcode/277/Null-Dot-Operator-Extension-Method) essentially the idea is that you can call your code and apply default options. I kinda like the idea, but I wasn't so convinced of the name he chose for the name of his extension method `_` it kinda looks like an operator, but really isn't and you have to start feeling for the guy reading your code 2 years after you left the company on this one.
Also, to use his code on nested properties you have to make a call like this:
    var result = bar._(b => b.ChildProperty)._(c => c.StringProperty);
Which I'm not sure is exactly attractive to the eye. Also, if you did try to chain a whole bunch of properties together it might end up really, really hard to read.
Now, it's easy to sit and snipe, but that's crappy, so my implementation of his idea gets called like this:
    var result = root.NeverNull((me) => me.SomeProperty.AnotherProperty.SomeProperty, defaultValue: new MyClass(3));
It's not exactly beautiful, but I think it's slightly more obvious. My implementation currently has the restriction that it only works for properties, not for methods. Anyway, the implementation is as follows, what do you think?
    public static class CodeInsipredByJeff
    {
        public static T NeverNull<T>(this object target, Expression<Func<T, T>> propertyExpression, T defaultValue) where T : class
        {
            var properties = GetProperties(GetExpression(propertyExpression));    
            object currentTarget = target;    
            foreach (var property in properties)            {                if (currentTarget == null)                {                    return defaultValue;                }    
                currentTarget = property.GetValue(currentTarget, null);            }    
            if (currentTarget != null & currentTarget is T)
            {
                return (T)currentTarget;
            }
            else
            {
                return defaultValue;
            }
        }    
        private static MemberExpression GetExpression<T>(Expression<Func<T>> expression)
        {
            var body = expression.Body;
            if (body is MemberExpression)
            {
                return body as MemberExpression;
            }
            else if (body is UnaryExpression)
            {
                var ue = body as UnaryExpression;
                return ue.Operand as MemberExpression;
            }
            else
            {
                throw new NotImplementedException();
            }
        }
    
        private static IEnumerable<PropertyInfo>GetProperties(Expression expression)
        {
            var memberExpression = expression as MemberExpression;
            if (memberExpression == null) yield break;
    
            var property = memberExpression.Member as PropertyInfo;    
            if (property == null)
            {
                throw new NotSupportedException("Expression is not a property accessor");
            }    
            foreach (var propertyInfo in GetProperties(memberExpression.Expression))
            {
                yield return propertyInfo;
            }    
            yield return property;
        }
    }
