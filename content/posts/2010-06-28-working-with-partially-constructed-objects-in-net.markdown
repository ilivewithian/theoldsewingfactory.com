---
layout: post
title: Working with partially constructed objects in .Net
date: 2010-06-28
---

Just a quick one, but what do you think will be the output of this program? It's not what I expected:



<pre lang="csharp">class Program
{
	static void Main()
	{
		CanILeak leakyRef = null;

		try
		{
			new CanILeak(cil => leakyRef = cil);
		}
		catch (Exception)
		{
			Console.WriteLine("Exception");
		}

		if (leakyRef != null)
			leakyRef.AreYouStillThere();

		Console.ReadLine();
	}
}

class CanILeak
{
	public CanILeak(Action fail)
	{
		fail(this);
		throw new Exception();
	}

	public void AreYouStillThere()
	{
		Console.WriteLine("I'm still here");
	}
}</pre>
