---
layout: post
title: A Gotcha when Comparing ImageFormat in .Net without TDD
date: 2011-01-13
---

I'm not there yet, but I'm working towards doing TDD / BDD for nearly all of my code. Even though I'm doing a lot of Web Forms development at the moment I'm discovering that it is still possible to drive a very large portion of my development through testing. Occasionally when I'm in a rush or the code seems very trivial I'll fall back into bad habits and code first, test later. Sadly later is often too late.

One such example was when I needed the mime type of an image that was passed to me as a byte array, since I know that the images are always going to be thumbnail sized and the result will be cached I wrote a method similar to this:

    public string GetMimeType(byte[] imageData)
    {
        ImageFormat format;
        using (var buffer = new MemoryStream(imageData))
        {
            var image = Image.FromStream(buffer);
            format = image.RawFormat;
        }
    
        if (format == ImageFormat.Png)
        {
            return "image/png";
        }
    
        // repeated for a bunch of mime types
        return "image/jpeg";
    
    }

Nothing extravagant, I tested it very briefly with a jpeg and moved on. The best way to show how wrong I was is to write the tests I should have written when I was feeling slack.

    [TestFixture]
    public class ImageFormatCompareTests
    {
        byte[] singlePixelPng = new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A, 0x00, 0x00, 0x00, 0x0D, 0x49, 
                                                0x48, 0x44, 0x52, 0x00, 0x00, 0x00, 0x01, 0x00, 0x00, 0x00, 0x01, 0x08, 0x02, 
                                                0x00, 0x00, 0x00, 0x90, 0x77, 0x53, 0xDE, 0x00, 0x00, 0x00, 0x01, 0x73, 0x52, 
                                                0x47, 0x42, 0x00, 0xAE, 0xCE, 0x1C, 0xE9, 0x00, 0x00, 0x00, 0x04, 0x67, 0x41, 
                                                0x4D, 0x41, 0x00, 0x00, 0xB1, 0x8F, 0x0B, 0xFC, 0x61, 0x05, 0x00, 0x00, 0x00, 
                                                0x09, 0x70, 0x48, 0x59, 0x73, 0x00, 0x00, 0x0E, 0xC3, 0x00, 0x00, 0x0E, 0xC3, 
                                                0x01, 0xC7, 0x6F, 0xA8, 0x64, 0x00, 0x00, 0x00, 0x0C, 0x49, 0x44, 0x41, 0x54, 
                                                0x18, 0x57, 0x63, 0xB0, 0xF7, 0x38, 0x03, 0x00, 0x02, 0x1D, 0x01, 0x54, 0x40, 
                                                0xC5, 0x12, 0x88, 0x00, 0x00, 0x00, 0x00, 0x49, 0x45, 0x4E, 0x44, 0xAE, 0x42, 
                                                0x60, 0x82 };
    
        private ImageFormat GetImageFormatForImage(byte[] imageData)
        {
            using(var buffer = new MemoryStream(imageData))
            {
                var image = Image.FromStream(buffer);
                return image.RawFormat;
            }
        }
    
        [Test]
        public void ComparePngUsingEqualsEquals_Fails()
        {
            //Arrange
            var formatFromPng = GetImageFormatForImage(singlePixelPng);
    
            //Act
            var areImageFormatsEqual = formatFromPng == ImageFormat.Png;
    
            //Assert
            Assert.That(areImageFormatsEqual, Is.True);
        }
    
        [Test]
        public void ComparePngUsingEqualsMethod_Suceeds()
        {
            //Arrange
            var formatFromPng = GetImageFormatForImage(singlePixelPng);
    
            //Act
            var areImageFormatsEqual = formatFromPng.Equals(ImageFormat.Png);
    
            //Assert
            Assert.That(areImageFormatsEqual, Is.True);
        }
    }

It turns out that the ImageFormat class has not overridden the == operator and since that's not implemented the runtime drops back to `Object.ReferenceEquals` which will always return false in my case. Simply switching my code over to using the .Equals fixes my problems and acts as a very simple wrap on the knuckles to not get lazy with TDD. Lesson re-learnt.
