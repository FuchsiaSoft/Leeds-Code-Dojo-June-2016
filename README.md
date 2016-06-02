# Leeds-Code-Dojo-June-2016
An exercise in Refuctoring (opposite of refactoring) for Leeds Code Dojo June 2016

Challenge was to take the Leap Year algorithm and add some refuctoring to make it difficult to maintain.

###Good Code:
```C#
    public class LeapYearChecker : ILeapYearChecker
    {
        public bool IsLeapYear(int year)
        {
            if (year % 4 != 0) return false;
            if (year % 100 != 0) return true;
            if (year % 400 != 0) return false;
            return true;
        }

        public bool IsLeapYear(DateTime date)
        {
            return IsLeapYear(date.Year);
        }
    }
```

###Bad Code:
```C#
//##############################################################################
//My Refuctorings...
//##############################################################################
//The "magic" of async   ---   Used an alias for Task and made it look like
//                             it was doing something other than running sync work

//Nesty McNest           ---   Added a bunch of unnecessary nesting

//Mirror Factory         ---   Something masquerading as a factory but actually
//                             just uses reflection in a weird way

//Work by exception      ---   Range check is pointless, as the integer overload will work
//                             for any year regardless of whether it makes a valid DateTime
//                             in .Net, and the DateTime overload wouldn't be callable without
//                             a valid DateTime object

//Casting                ---   Multiple types of casting, because it's annoying

//Hidden class           ---   Two classes declared in same file, because C# devs
//                             hate that

//WET code               ---   Opposite of DRY, code duplicated where it was a nice
//                             neat bit of overloading
//##############################################################################
public static class LeapYearCheckerFactory
    {
        public static ILeapYearChecker CreateLeapYearChecker()
        {
            Type checkerType = Type.GetType("LeapYearChecking.BadLeapYearChecker,LeapYearChecker");
            
            BadLeapYearChecker checkerConcrete = ((BadLeapYearChecker)Activator.CreateInstance(checkerType));

            return checkerConcrete.GetMe();
        }
    }
    public class BadLeapYearChecker : ILeapYearChecker
    {
        public ILeapYearChecker GetMe()
        {
            return (ILeapYearChecker)
                Activator.CreateInstance(typeof(BadLeapYearChecker));
        }

        public bool IsLeapYear(int year)
        {
            try
            {
                DateTime temp = new DateTime(year, 1, 1);
            }
            catch (Exception e)
            {
                throw e;
            }
            return IsLeapYearAsync(year).Result;
        }


        private async Task<bool> IsLeapYearAsync(int year)
        {
            if (await Magic.Run<bool>(() => 
            { if (year % 4 != 0)
                return true;
                return false;
            }))
            {
                return false;
            }
            else
            {
                if (await Magic.Run<bool>(() =>
                {
                    if (year % 100 != 0)
                    return true;
                    return false;
                }))
                {
                    return true;
                }
                else
                {
                    if (await Magic.Run<bool>(() =>
                    {
                        if (year % 400 != 0)
                        return true;
                        return false;
                    }))
                    {
                        return false;
                    }
                    else
                    {
                        return true;
                    }
                }
            }
        }

        public bool IsLeapYear(DateTime date)
        {
            return IsLeapYearAsync(date).Result;
        }
        private async Task<bool> IsLeapYearAsync(DateTime date)
        {
            if (await Magic.Run<bool>(() =>
            {
                if (date.Year % 4 != 0)
                { return true; }
                return false;
            }))
            {
                return false;
            }
            else
            {
                if (await Magic.Run<bool>(() =>
                {
                    if (date.Year % 100 != 0)
                    { return true; }
                    return false;
                }))
                {
                    return true;
                }
                else
                {
                    if (await Magic.Run<bool>(() =>
                    {
                        if (date.Year % 400 != 0)
                        { return true; }
                        return false;
                    }))
                    {
                        return false;
                    }
                    else
                    {
                        return true;
                    }
                }
            }
        }
    }
```
