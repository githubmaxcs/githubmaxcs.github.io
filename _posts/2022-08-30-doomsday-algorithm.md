---
layout: post
title: Doomsday Algorithm
subtitle: Calculate the Day of the Week for a Given Date
tags: [mathematics, modular arithmetic, doomsday algorithm, python]
---

Suppose we wish to determine the day of the week for a given date, say, June 16, 1904. We can, of course, search for the day of the week using Google, but we don’t need Google! We can use the [**Doomsday Algorithm**](https://en.wikipedia.org/wiki/Doomsday_rule). The Doomsday Algorithm, devised by the English mathematician [John Conway]( https://en.wikipedia.org/wiki/John_Horton_Conway), is a method to determine the day of the week for a given date, and in this post we’ll learn how to determine that June 16, 1904 was a Thursday.

## Gregorian Calendar

The [Gregorian calendar]( https://en.wikipedia.org/wiki/Gregorian_calendar) was introduced by Pope Gregory XIII in October 1582, supplanting the [Julian calendar]( https://en.wikipedia.org/wiki/Julian_calendar), and that year Thursday, October 4, 1582 was followed by Friday, October 15, 1582. So, why switch calendars? Well, the motivation was the **solar year**, or the time it takes the Earth to orbit the Sun, measured between successive spring or autumn equinoxes, or summer or winter solstices. The Julian calendar assumed the average solar year had 365.25 days, hence a normal year had 365 days, and a **leap year** (every four years) had 366 days, with no exceptions; however, an actual solar year, though it varies, has roughly 365.24219 days, meaning the Julian calendar gained a day every 128 years: 

$$\frac{1}{(356.25 – 365.24219)} \approx 128.$$

Thus, to prevent the drift of the calendar, the Gregorian system implements leap years for years divisible by four, except for years divisible by 100, unless those years divisible by 100 are also divisible by 400. For example, 1700, 1800, and 1900 are _not_ leap years, but 2000 _is_ a leap year. As a result, the average calendar year over a 400-year period is shortened by 0.0075 days, becoming 365.2425 days (a value closer to the actual length of a solar year):

$$\frac{1}{400} \left( (365 \times 400) + (1 \times \frac{400}{4}) - (1 \times \frac{400}{100}) + (1 \times \frac{400}{400}) \right) = 365.2425.$$

## Modular Arithmetic

For a more detailed look at modular arithmetic, see [_Wikipedia_](https://en.wikipedia.org/wiki/Modular_arithmetic). 

Here, we consider integers modulo 7, letting $n$ be an integer (\\(n \in \mathbb{Z}\\), where \\(\mathbb{Z}\\) is the set of integers):


| Equivalence Class | \\(n = 0\\)                    | \\(n = 1\\)                         |  \\(n = -1\\)                   | cont’d |
| :-------------------:   | :-----------------------: | :------------------------: | :------------------------:   | :---: |
| \\(\{0 + 7(n)\}\\)       | 0 \\(\equiv\\) 0 mod(7)  | 7 \\(\equiv\\) 0 mod(7)   | -7 \\(\equiv\\) 0 mod(7)  | ...     |
| \\(\{1 + 7(n)\}\\)       | 1 \\(\equiv\\) 1 mod(7)  | 8 \\(\equiv\\) 1 mod(7)   | -6 \\(\equiv\\) 0 mod(7)  | ...     |
| \\(\{2 + 7(n)\}\\)       | 2 \\(\equiv\\) 2 mod(7)  | 9 \\(\equiv\\) 2 mod(7)   | -5 \\(\equiv\\) 0 mod(7)  | ...     |
| \\(\{3 + 7(n)\}\\)       | 3 \\(\equiv\\) 3 mod(7)  | 10 \\(\equiv\\) 3 mod(7) | -4 \\(\equiv\\) 0 mod(7)  | ...     |
| \\(\{4 + 7(n)\}\\)       | 4 \\(\equiv\\) 4 mod(7)  | 11 \\(\equiv\\) 4 mod(7) | -3 \\(\equiv\\) 0 mod(7)  | ...     |
| \\(\{5 + 7(n)\}\\)       | 5 \\(\equiv\\) 5 mod(7)  | 12 \\(\equiv\\) 5 mod(7) | -2 \\(\equiv\\) 0 mod(7)  | ...     |
| \\(\{6 + 7(n)\}\\)       | 6 \\(\equiv\\) 6 mod(7)  | 13 \\(\equiv\\) 6 mod(7) | -1 \\(\equiv\\) 0 mod(7)  | ...     |

So, 0, 7, -7, 14, -14, and so on, are **congruent**, or equivalent, modulo 7. In total, we have seven **equivalence classes** for integers modulo 7.

Since we have seven days in a week, we label the days as follows:

| Day                                          | Index |
| :----------------------------------: | :---: |
| Sunday <br> (Noneday)         |   0   |
| Monday <br> (Oneday)          |   1   |
| Tuesday <br> (Twosday)        |   2   |	
| Wednesday <br> (Threesday) |   3   |
| Thursday <br> (Foursday)      |   4   |
| Friday <br> (Fiveday)             |   5   |
| Saturday <br> (Sixday)           |   6   |

Thus, if today is Monday (1), in seven days, of course, we will return to Monday: \\(1 + 7 = 8 \equiv 1 \ \text{mod(}7\text{)}\\). Presto! 

Further, we observe that a one-year period (without a leap year) results in a one-day offset:

$$365 \equiv 1 \ \text{mod(}7\text{)},$$

whereas a 400-year period results in a zero-day offset, accounting for the Gregorian system of leap years:

$$400 + \frac{400}{4} – \frac{400}{100} + \frac{400}{400} \equiv 0 \ \text{mod(}7\text{)}.$$

## Doomsday Algorithm

Now, having the background, we arrive at the Doomsday Algorithm.

First, in any given year, there are dates (we’ll call these **doomsday dates**) which fall on the same day of the week, and in a given year, said day of the week is called the **doomsday**. Really, we could choose other sets of dates, but there are easy-to-remember doomsday dates:

| Month        | M/D  | Mnemonic                | Complete List |
| :----------- | :----- | :----------------------- | :--- |
| January.     | 1/3 (Normal Year) <br> 1/4 (Leap Year) | “3 for 3 out of 4; 4 for 4th” | {1/3, 1/10, 1/17, 1/24, 1/31} (Normal Year) <br> {1/4, 1/11, 1/18, 1/25} (Leap Year) |
| February    | 2/28 (Normal Year) <br> 2/29 (Leap Year) | “Last day of February, or March 0” | {2/7, 2/14, 2/21, 2/28} (Normal Year) <br> {2/1, 2/8, 2/15, 2/22, 2/29} (Leap Year) |
| March        | 3/14 | “Pi Day” | {3/7, 3/14, 3/21, 3/28} |
| April          | 4/4 | “4/4, 6/6, 8/8, 10/10, 12/12” | {4/4, 4/11, 4/18, 4/25} |
| May           | 5/9 | “9-to-5 at 7/11” | {5/2, 5/9, 5/16, 5/23, 5/30} |
| June           | 6/6 | “4/4, 6/6, 8/8, 10/10, 12/12” | {6/6, 6/13, 6/20, 6/27} |
| July            | 7/11 | “9-to-5 at 7/11” | {7/4, 7/11, 7/18, 7/25} |
| August       | 8/8 | “4/4, 6/6, 8/8, 10/10, 12/12” | {8/1, 8/8, 8/15, 8/22, 8/29} |
| September  | 9/5 | “9-to-5 at 7/11” | {9/5, 9/12, 9/19, 9/26} |
| October      |10/10 | “4/4, 6/6, 8/8, 10/10, 12/12” | {10/3, 10/10, 10/17, 10/24, 10/31} |
| November  | 11/7 | “9-to-5 at 7/11” | {11/7, 11/14, 11/21, 11/28} |
| December  | 12/12 | “4/4, 6/6, 8/8, 10/10, 12/12” | {12/5, 12/12, 12/19, 12/26} |

For example, in a given year, if the doomsday is Monday (with respect to our doomsday dates), every doomsday date above is a Monday, and knowing a one-year period (without a leap year) results in a one-day offset, the following year's doomsday would be Tuesday.

Second, we establish the doomsday for each centurial year (e.g., 1500, 1600, 1700, etc.). Once we have the doomsday for each centurial year, we can use modular arithmetic to determine the doomsday for any year in a given century, from which we can determine the day of the week of a given date. 

Now, I'm writing as of August 30, 2022, so the nearest doomsday date is August 29, 2022 (8/22), which I know was a Monday. Thus, the doomsday for the year 2022 is Monday. Further, there have been 5 leap years since 2000, so we obtain the offset from 2000 to 2022:

$$22 + \lfloor \frac{22}{4} \rfloor = 22 + 5 = 27 \equiv 6 \ \text{mod(}7\text{)} \equiv -1 \ \text{mod(}7\text{)},$$

where \\(\lfloor x \rfloor\\) ("floor of x") is the greatest integer smaller than \\(x\\). We find the doomsday for 2000 is Tuesday, since Monday (1) + 1 is Tuesday (2). Repeating the process, noting that 2000 _is_ a leap year, we obtain the offset from 1900 to 2000:

$$100 + \lfloor \frac{100}{4} \rfloor = 100 + 25 \equiv 6 \ \text{mod(}7\text{)} \equiv -1 \ \text{mod(}7\text{)}$$

We find the doomsday for 1900 is Wednesday, since Tuesday (2) + 1 is Wednesday (3). Again, repeating the process, noting that 1900 is _not_ a leap year, we obtain the offset from 1800 to 1900:

$$100 + \lfloor \frac{100}{4} \rfloor - \lfloor \frac{100}{100} \rfloor = 100 + 25 - 1 \equiv 5 \ \text{mod(}7\text{)} \equiv -2 \ \text{mod(}7\text{)}$$

We find the doomsday for 1800 is Friday, since Wednesday (3) + 2 is Friday (5). We can repeat the process for each centurial year, obtaining the following results:

| Wednesday | Tuesday | Sunday | Friday |
| :---:     | :---:   | :---:  | :---:  |
| ...       | ...     | ...    | ...    |
| 1500      | 1600    | 1700   | 1800   |
| 1900      | 2000    | 2100   | 2200   |
| ...       | ...     | ...    | ...    |

Finally, here are the steps of the Doomsday Algorithm:
1. Given a date, consider the century, and determine the doomsday, X, for that century. For June 16, 1904, the doomsday for 1900 is Wednesday (X = 3).
2. Take the year of the given century, Y, and determine the doomsday for that year. For June 16, 1904 (Y = 4): $$ X + Y + \lfloor \frac{Y}{4} \rfloor = 3 + 4 + 1 \equiv 1 \ \text{mod(}7\text{)} $$ Thus, the doomsday for 1904 is Monday (1).
3. Find the nearest doomsday date, and add/subtract to the given date. For June 16, 1904, 6/6 and 6/13 are doomsday dates, meaning June 13, 1904 was a Monday (1), so June 16, 1904 was a Thursday (4), since \\(1 + (16 - 13) \equiv 4 \ \text{mod(}7\text{)}\\).

## Python Implementation

{% highlight Python linenos %}
day_dictionary = {'0':'Sunday', 'Sunday':'0', '1':'Monday', 'Monday':'1',\
                  '2':'Tuesday', 'Tuesday':'2', '3':'Wednesday', 'Wednesday':'3',\
                  '4':'Thursday', 'Thursday':'4', '5':'Friday', 'Friday':'5',\
                  '6':'Saturday', 'Saturday':'6'}

ddates_normal = ['1/3', '1/10', '1/17', '1/24', '1/31', '2/7', '2/14', '2/21', '2/28',\
                  '3/7', '3/14', '3/21', '3/28', '4/4', '4/11', '4/18', '4/25', '5/2',\
                  '5/9', '5/16', '5/23', '5/30', '6/6', '6/13', '6/20', '6/27', '7/4',\
                  '7/11', '7/18', '7/25', '8/1', '8/8', '8/15', '8/22', '8/29', '9/5',\
                  '9/12', '9/19', '9/26', '10/3', '10/10', '10/17', '10/24', '10/31',\
                  '11/7', '11/14', '11/21', '11/28', '12/5', '12/12', '12/19', '12/26']
ddates_leap = ['1/4', '1/11', '1/18', '1/25', '2/1', '2/8', '2/15', '2/22', '2/29',\
                  '3/7', '3/14', '3/21', '3/28', '4/4', '4/11', '4/18', '4/25', '5/2',\
                  '5/9', '5/16', '5/23', '5/30', '6/6', '6/13', '6/20', '6/27', '7/4',\
                  '7/11', '7/18', '7/25', '8/1', '8/8', '8/15', '8/22', '8/29', '9/5',\
                  '9/12', '9/19', '9/26', '10/3', '10/10', '10/17', '10/24', '10/31',\
                  '11/7', '11/14', '11/21', '11/28', '12/5', '12/12', '12/19', '12/26']

def anchor(date):
    """The anchor function accepts a date as a string, 'M/D/YYYY', and returns the doomsday of the
    corresponding centurial year. For example, passing '10/5/1582' will return 'Wednesday',
    since the corresponding centurial year is '1500'."""
    
    date_list = date.split('/')
    century = date_list[2][0:2] + '00'
    
    if ((int(century) - 1500)%400 == 0):
        return 'Wednesday'
    elif ((int(century) - 1600)%400 == 0):
        return 'Tuesday'
    elif ((int(century) - 1700)%400 == 0):
        return 'Sunday'
    elif ((int(century) - 1800)%400 == 0):
        return 'Friday'
    
def is_leap(date):
    """The is_leap function accepts a date as a string, 'M/D/YYYY', and returns True/False if the year, 
    YYYY, is/is not, respectively, a leap year. For example, passing May 4, 1900 will return False."""
    
    date_list = date.split('/')
    year = date_list[2]
    
    # years not divisible by 4 #
    if (int(year)%4 != 0):
        return False
    
    # years divisible by 4, but not 100 #
    if ((int(year)%4 == 0) & (not (int(year)%100 == 0))):
        return True
    
    # years divisible by 4 and 100, but not 400 #
    if ((int(year)%4 == 0) & (int(year)%100 == 0) & (not (int(year)%400 == 0))):
        return False
    
    # years divisible by 4, 100, and 400 #
    if ((int(year)%4 == 0) & (int(year)%100 == 0) & (int(year)%400 == 0)):
        return True

def doomsday_algorithm(date):
    """The doomsday_algorithm function accepts a date as a string, 'M/D/YYYY', and returns the day of the week. 
    For example, passing '6/4/1904 will return 'Thursday'."""
    
    date_list = date.split('/')
    anchor_value = int(day_dictionary[anchor(date)])
    (month, day, year_of_century) = (date_list[0], date_list[1], date_list[2][2:4])
    
    # for a normal (non-leap) year #
    if (not is_leap(date)):
        
        year_doomsday = (anchor_value + (int(year_of_century) + (int(year_of_century)//4)))%7
        
        ddates_same_month = []
        for elem in ddates_normal:
            compare = elem.split('/')
            if (compare[0] == month):
                ddates_same_month.append(elem)        
        
        ddates_distance = []
        for elem in ddates_same_month:
            compare = elem.split('/')
            ddates_distance.append(abs(int(compare[1]) - int(day)))
        
        day_of_week = day_dictionary[str((year_doomsday + min(ddates_distance))%7)]
        print(day_of_week)
            
    
    # for a leap year #
    elif (is_leap(date)):
        
        year_doomsday = (anchor_value + (int(year_of_century) + (int(year_of_century)//4)))%7
        
        ddates_same_month = []
        for elem in ddates_leap:
            compare = elem.split('/')
            if (compare[0] == month):
                ddates_same_month.append(elem)        
        
        ddates_distance = []
        for elem in ddates_same_month:
            compare = elem.split('/')
            ddates_distance.append(abs(int(compare[1]) - int(day)))
        
        day_of_week = day_dictionary[str((year_doomsday + min(ddates_distance))%7)]
        print(day_of_week)
{% endhighlight %}
