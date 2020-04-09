---
layout: post
title: So you'd Like Implicit Casting in Moq?
date: 2010-12-19
---

I love using Moq, it's great and it saves me a huge amount of time and effort when I'm writing unit tests. There's only thing that bothers me about it. The Object property on a mock, why is this even needed? Surely the judicious use of the implicit keyword and you could have a cleaner API, for example (coded in a text editor, I haven't even tried to compile it):

    var mockFileSystem = new Mock<T>();
    var objectUnderTest = new FileBackedThingy(mockFileSystem.Object);
    objectUnderTest.Save();
    mockFileSystem.Verify(x => x.SaveFile(It.IsAny()), Times.Once);

Could be replaced with:

    var mockFileSystem = new Mock<T>();
    var objectUnderTest = new FileBackedThingy(mockFileSystem);
    objectUnderTest.Save();
    mockFileSystem.Verify(x => x.SaveFile(It.IsAny()), Times.Once);

Now, I know it doesn't really make much difference, but it does make things a little nicer to my eyes. So it's Sunday afternoon, my wife's got rehearsals and gigs all day. What's a geek to do?

Never having seen the Moq codebase before I thought it might take a little while to find what I needed to do, I was wrong, in about 90 seconds I found the Moq.Mock class and thought I'd just need to add something along the lines of:

    public static implicit operator T(Mock mock)
    {
        return mock.Object;
    }

A really simple change, weirdly the code is already there, but commented out. Since commented out code is one of my programmer pet hates and it was code I was hoping for, this was a bit like a red rag to a bull. Fortunately there was a reasonably sensible comment justifying this heinous action:

    // NOTE: known issue. See https://connect.microsoft.com/VisualStudio/feedback/ViewFeedback.aspx?FeedbackID=318122

The complaint is a simple one, C# won't let you have user defined casts between interface types. Also, they won't let us have it, ever. So the lesson learned? No idea, but I think Bravo is showing the monster trucks, so I might watch that and snooze before I go out.
