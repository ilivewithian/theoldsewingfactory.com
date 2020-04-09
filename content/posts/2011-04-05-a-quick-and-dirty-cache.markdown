---
layout: post
title: A Quick and Dirty Cache
date: 2011-04-05
---

Brownfield development on a tight deadline is never fun, it's never elegant and it's certainly not satisfying.

One problem I keep finding is code that repeatedly calls the backing stores with the same query. If I don't have the time to refactor the code to work properly I sometimes cheat and use a cache that's specific to the HttpContext. It's not nice, but it does get me out of a hole every once in a while.

    public static class ContextCache
    {
    	public static T Get<T>(string key)
    	{
    		var value = HttpContext.Current.Items[key];
    		return value is T ? (T)value : default(T);
    	}
    
    	public static void Put(string key, object value)
    	{
    		HttpContext.Current.Items[key] = value;
    	}
    }

It's pretty simple to use:

    var entity = ContextCache.Get<Entity>("Entity_123");
    if (entity == null)
    {
        entity = GetFromBackingStore(123);
        ContextCache.Put("Entity_123", entity);
    }
    return entity;

This is definitely one of those bits of ugly code that can get you out of a hole in a rush. It aint pretty, but it works.
