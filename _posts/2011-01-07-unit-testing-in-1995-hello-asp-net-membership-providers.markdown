---
layout: post
title: Unit Testing in 1995, Hello ASP .Net Membership Providers
---

Among the many wonders that is .Net we have the task parallel library, linq, a first class garbage collector and the legend that is Jon Skeet. If, however, you have the crazy desire to unit test code that relies on `System.Web.Security.Membership` you're in for a world of pain. To ease that pain slightly I've created an interface to hide away all that static class nastiness. Now mock out the behaviours that you need to test and pretend all is right with the world.
    using System.Web.Security;
    using System.Web.Configuration;    
    public interface IMembershipWrapper
    {
        MembershipUser CreateUser(string username, string password);
        MembershipUser CreateUser(string username, string password, string email);
        MembershipUser CreateUser(string username, string password, string email, string passwordQuestion, string passwordAnswer, bool isApproved, out MembershipCreateStatus status);
        MembershipUser CreateUser(string username, string password, string email, string passwordQuestion, string passwordAnswer, bool isApproved, object providerUserKey, out MembershipCreateStatus status);
        bool DeleteUser(string username);
        bool DeleteUser(string username, bool deleteAllRelatedData);
        MembershipUserCollection FindUsersByEmail(string emailToMatch);
        MembershipUserCollection FindUsersByEmail(string emailToMatch, int pageIndex, int pageSize, out int totalRecords);
        MembershipUserCollection FindUsersByName(string usernameToMatch);
        MembershipUserCollection FindUsersByName(string usernameToMatch, int pageIndex, int pageSize, out int totalRecords);
        string GeneratePassword(int length, int numberOfNonAlphanumericCharacters);
        MembershipUserCollection GetAllUsers();
        MembershipUserCollection GetAllUsers(int pageIndex, int pageSize, out int totalRecords);
        int GetNumberOfUsersOnline();
        MembershipUser GetUser();
        MembershipUser GetUser(bool userIsOnline);
        MembershipUser GetUser(object providerUserKey);
        MembershipUser GetUser(string username);
        MembershipUser GetUser(object providerUserKey, bool userIsOnline);
        MembershipUser GetUser(string username, bool userIsOnline);
        string GetUserNameByEmail(string emailToMatch);
        void UpdateUser(MembershipUser user);
        bool ValidateUser(string username, string password);
    }
    public class MembershipWrapper : IMembershipWrapper
    {
        public MembershipUser CreateUser(string username, string password)
        {
            return Membership.CreateUser(username, password);
        }    
        public MembershipUser CreateUser(string username, string password, string email)
        {
            return Membership.CreateUser(username, password, email);
        }    
        public MembershipUser CreateUser(string username, string password, string email, string passwordQuestion, string passwordAnswer, bool isApproved, out MembershipCreateStatus status)
        {
            return Membership.CreateUser(username, password, email, passwordQuestion, passwordAnswer, isApproved, out status);
        }    
        public MembershipUser CreateUser(string username, string password, string email, string passwordQuestion, string passwordAnswer, bool isApproved, object providerUserKey, out MembershipCreateStatus status)
        {
            return Membership.CreateUser(username, password, email, passwordQuestion, passwordAnswer, isApproved, providerUserKey, out status);
        }    
        public bool DeleteUser(string username)
        {
            return Membership.DeleteUser(username);
        }    
        public bool DeleteUser(string username, bool deleteAllRelatedData)
        {
            return Membership.DeleteUser(username, deleteAllRelatedData);
        }    
        public MembershipUserCollection FindUsersByEmail(string emailToMatch)
        {
            return Membership.FindUsersByEmail(emailToMatch);
        }    
        public MembershipUserCollection FindUsersByEmail(string emailToMatch, int pageIndex, int pageSize, out int totalRecords)
        {
            return Membership.FindUsersByEmail(emailToMatch, pageIndex, pageSize, out totalRecords);
        }    
        public MembershipUserCollection FindUsersByName(string usernameToMatch)
        {
            return Membership.FindUsersByName(usernameToMatch);
        }    
        public MembershipUserCollection FindUsersByName(string usernameToMatch, int pageIndex, int pageSize, out int totalRecords)
        {
            return Membership.FindUsersByName(usernameToMatch, pageIndex, pageSize, out totalRecords);
        }
    
        public string GeneratePassword(int length, int numberOfNonAlphanumericCharacters)
        {
            return Membership.GeneratePassword(length, numberOfNonAlphanumericCharacters);
        }    
        public MembershipUserCollection GetAllUsers()
        {
            return Membership.GetAllUsers();
        }    
        public MembershipUserCollection GetAllUsers(int pageIndex, int pageSize, out int totalRecords)
        {
            return Membership.GetAllUsers(pageIndex, pageSize, out totalRecords);
        }    
        public int GetNumberOfUsersOnline()
        {
            return Membership.GetNumberOfUsersOnline();
        }    
        public MembershipUser GetUser()
        {
            return Membership.GetUser();
        }    
        public MembershipUser GetUser(bool userIsOnline)
        {
            return Membership.GetUser(userIsOnline);
        }    
        public MembershipUser GetUser(object providerUserKey)
        {
            return Membership.GetUser(providerUserKey);
        }    
        public MembershipUser GetUser(string username)
        {
            return Membership.GetUser(username);
        }    
        public MembershipUser GetUser(object providerUserKey, bool userIsOnline)
        {
            return Membership.GetUser(providerUserKey, userIsOnline
        }    
        public MembershipUser GetUser(string username, bool userIsOnline)
        {
            return Membership.GetUser(username, userIsOnline);
        }    
        public string GetUserNameByEmail(string emailToMatch)
        {
            return Membership.GetUserNameByEmail(emailToMatch);
        }    
        public void UpdateUser(MembershipUser user)
        {
            Membership.UpdateUser(user);
        }    
        public bool ValidateUser(string username, string password)
        {
            return Membership.ValidateUser(username, password);
        }    
    }
