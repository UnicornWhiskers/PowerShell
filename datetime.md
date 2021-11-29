## Converting to datetime, a practical example

Reference:
 - [PowerShell Convert String to Date](https://www.educba.com/powershell-convert-string-to-date/)

I've pulled down indicators from Microsoft Defender Endpoint using the API, ex: "https://api.securitycenter.microsoft.com/api/indicators"

Since I'm trying to purge out older indicators, i'd like to try to get a count of indicators grouped by date.

The data structure is like so (this is system object converted from json)
```
id                      : 99999
indicatorValue          : 36bcbd6cc543d93d36915ecaa4225c000816c6099955abfc5a6ddfee83c8a496
indicatorType           : FileSha256
action                  : Alert
createdBy               : AAAAA-BBBB-CCCC-1A1A-AA22BB33CC
severity                : High
category                : 1
application             :
educateUrl              : 
bypassDurationHours     :
title                   : Indicator 36bcbd6cc543d93d36915ecaa4225c000816c6099955abfc5a6ddfee83c8a496 of type FileSha256
description             : Test indicator from VT
recommendedActions      :
creationTimeDateTimeUtc : 2021-11-17T15:32:42.9602762Z
expirationTime          : 2022-02-15T15:20:22Z
lastUpdateTime          : 2021-11-17T15:32:43.213243Z
lastUpdatedBy           :
rbacGroupNames          : {}
rbacGroupIds            : {}
notificationId          :
notificationBody        :
version                 :
mitreTechniques         : {}
historicalDetection     : False
lookBackPeriod          :
generateAlert           : True
additionalInfo          :
createdByDisplayName    : VT_testing
externalId              : 11111111111
createdBySource         : PublicApi
certificateInfo         :
```

So to convert the date string to datetime:

```
PS E:\> $mytime = ($indicators | select-object -first 1 -Property creationTimeDateTimeUtc).creationTimeDateTimeUtc

PS E:\> $mytime
2021-11-17T15:32:42.9602762Z

PS E:\> $mytime.getType()

IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     String                                   System.Object
```
So the creationTimeDateTimeUtc property is of type String

```
PS E:\> $newtime = [System.DateTime]::ParseExact($mytime,'yyyy-MM-dd\THH:mm:ss.fffffffZ',$null)

PS E:\> $newtime.gettype()


IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     DateTime                                 System.ValueType
```

I would like to convert the DateTime object to something shorter for grouping.  So Looking at the methods...

```
PS E:\> $newtime | Get-Member

   TypeName: System.DateTime

Name                 MemberType     Definition
----                 ----------     ----------
Add                  Method         datetime Add(timespan value)
AddDays              Method         datetime AddDays(double value)
AddHours             Method         datetime AddHours(double value)
AddMilliseconds      Method         datetime AddMilliseconds(double value)
AddMinutes           Method         datetime AddMinutes(double value)
AddMonths            Method         datetime AddMonths(int months)
AddSeconds           Method         datetime AddSeconds(double value)
AddTicks             Method         datetime AddTicks(long value)
AddYears             Method         datetime AddYears(int value)
CompareTo            Method         int CompareTo(System.Object value), int CompareTo(datetime value), int IComparable.CompareTo(System.Object obj), int IComparable[datetime].CompareTo(datetime other)
Equals               Method         bool Equals(System.Object value), bool Equals(datetime value), bool IEquatable[datetime].Equals(datetime other)
GetDateTimeFormats   Method         string[] GetDateTimeFormats(), string[] GetDateTimeFormats(System.IFormatProvider provider), string[] GetDateTimeFormats(char format), string[] GetDateTimeFormats(char format, System.IFormatProvider provider)
GetHashCode          Method         int GetHashCode()
GetObjectData        Method         void ISerializable.GetObjectData(System.Runtime.Serialization.SerializationInfo info, System.Runtime.Serialization.StreamingContext context)
GetType              Method         type GetType()
GetTypeCode          Method         System.TypeCode GetTypeCode(), System.TypeCode IConvertible.GetTypeCode()
IsDaylightSavingTime Method         bool IsDaylightSavingTime()
Subtract             Method         timespan Subtract(datetime value), datetime Subtract(timespan value)
ToBinary             Method         long ToBinary()
ToBoolean            Method         bool IConvertible.ToBoolean(System.IFormatProvider provider)
ToByte               Method         byte IConvertible.ToByte(System.IFormatProvider provider)
ToChar               Method         char IConvertible.ToChar(System.IFormatProvider provider)
ToDateTime           Method         datetime IConvertible.ToDateTime(System.IFormatProvider provider)
ToDecimal            Method         decimal IConvertible.ToDecimal(System.IFormatProvider provider)
ToDouble             Method         double IConvertible.ToDouble(System.IFormatProvider provider)
ToFileTime           Method         long ToFileTime()
ToFileTimeUtc        Method         long ToFileTimeUtc()
ToInt16              Method         int16 IConvertible.ToInt16(System.IFormatProvider provider)
ToInt32              Method         int IConvertible.ToInt32(System.IFormatProvider provider)
ToInt64              Method         long IConvertible.ToInt64(System.IFormatProvider provider)
ToLocalTime          Method         datetime ToLocalTime()
ToLongDateString     Method         string ToLongDateString()
ToLongTimeString     Method         string ToLongTimeString()
ToOADate             Method         double ToOADate()
ToSByte              Method         sbyte IConvertible.ToSByte(System.IFormatProvider provider)
ToShortDateString    Method         string ToShortDateString()
ToShortTimeString    Method         string ToShortTimeString()
ToSingle             Method         float IConvertible.ToSingle(System.IFormatProvider provider)
ToString             Method         string ToString(), string ToString(string format), string ToString(System.IFormatProvider provider), string ToString(string format, System.IFormatProvider provider), string IFormattable.ToString(string format, System.IFormatProvider formatProvider), stri...
ToType               Method         System.Object IConvertible.ToType(type conversionType, System.IFormatProvider provider)
ToUInt16             Method         uint16 IConvertible.ToUInt16(System.IFormatProvider provider)
ToUInt32             Method         uint32 IConvertible.ToUInt32(System.IFormatProvider provider)
ToUInt64             Method         uint64 IConvertible.ToUInt64(System.IFormatProvider provider)
ToUniversalTime      Method         datetime ToUniversalTime()
Date                 Property       datetime Date {get;}
Day                  Property       int Day {get;}
DayOfWeek            Property       System.DayOfWeek DayOfWeek {get;}
DayOfYear            Property       int DayOfYear {get;}
Hour                 Property       int Hour {get;}
Kind                 Property       System.DateTimeKind Kind {get;}
Millisecond          Property       int Millisecond {get;}
Minute               Property       int Minute {get;}
Month                Property       int Month {get;}
Second               Property       int Second {get;}
Ticks                Property       long Ticks {get;}
TimeOfDay            Property       timespan TimeOfDay {get;}
Year                 Property       int Year {get;}
DateTime             ScriptProperty System.Object DateTime {get=if ((& { Set-StrictMode -Version 1; $this.DisplayHint }) -ieq  "Date")...
```

So to confirm I'll putput the value of the ToShortDateString property

```
PS E:\> $newtime.ToShortDateString()

2021-11-17
```

So one of the ways I can think to convert each date as we iterate through the indicators pulled down from the API is using an inline custom object with the value of the datetime object outpue as a Short Date format.  There are probably better ways if you can
find them on stack exchange...
```
$indicators | select-object @{Name="time";Expression={([System.DateTime]::ParseExact($_.creationTimeDateTimeUtc,'yyyy-MM-dd\THH:mm:ss.fffffffZ',$null)).ToShortDateString()}}

2021-11-08
2021-11-08
2021-11-08
2021-11-08
2021-11-08
2021-11-08
2021-11-08
2021-11-08
2021-11-08
2021-11-08
2021-11-08
...
```

So with this output I  should be able to group these by the new "time" property using the  **group-object** cmdlet
```
PS E:\> $indicators | select-object @{Name="time";Expression={([System.DateTime]::ParseExact($_.creationTimeDateTimeUtc,'yyyy-MM-dd\THH:mm:ss.fffffffZ',$null)).ToShortDateString()}} | Group-Object -Property time


Count Name                      Group
----- ----                      -----
  578 2021-11-17                {@{time=2021-11-17}, @{time=2021-11-17}, @{time=2021-11-17}, @{time=2021-11-17}...}
 1025                           {@{time=}, @{time=}, @{time=}, @{time=}...}
  177 2021-11-16                {@{time=2021-11-16}, @{time=2021-11-16}, @{time=2021-11-16}, @{time=2021-11-16}...}
    1 2021-11-15                {@{time=2021-11-15}}
   12 2021-11-13                {@{time=2021-11-13}, @{time=2021-11-13}, @{time=2021-11-13}, @{time=2021-11-13}...}
  561 2021-11-12                {@{time=2021-11-12}, @{time=2021-11-12}, @{time=2021-11-12}, @{time=2021-11-12}...}
  831 2021-11-11                {@{time=2021-11-11}, @{time=2021-11-11}, @{time=2021-11-11}, @{time=2021-11-11}...}
 2467 2021-11-10                {@{time=2021-11-10}, @{time=2021-11-10}, @{time=2021-11-10}, @{time=2021-11-10}...}
  522 2021-11-09                {@{time=2021-11-09}, @{time=2021-11-09}, @{time=2021-11-09}, @{time=2021-11-09}...}
 1489 2021-11-08                {@{time=2021-11-08}, @{time=2021-11-08}, @{time=2021-11-08}, @{time=2021-11-08}...}
  503 2021-11-07                {@{time=2021-11-07}, @{time=2021-11-07}, @{time=2021-11-07}, @{time=2021-11-07}...}
  382 2021-11-06                {@{time=2021-11-06}, @{time=2021-11-06}, @{time=2021-11-06}, @{time=2021-11-06}...}
  176 2021-11-05                {@{time=2021-11-05}, @{time=2021-11-05}, @{time=2021-11-05}, @{time=2021-11-05}...}
  504 2021-11-04                {@{time=2021-11-04}, @{time=2021-11-04}, @{time=2021-11-04}, @{time=2021-11-04}...}
  772 2021-11-03                {@{time=2021-11-03}, @{time=2021-11-03}, @{time=2021-11-03}, @{time=2021-11-03}...}
```

That is basically what I was looking to do.

You can also get a count for all indicators older then a certain date.  This also seems like an alternative to using the ParseExact method if precision is not critical.

First we're looking for items where the creationTimeDateTimeUtc property (converted to datetime object) is less then 2021-11-17, and using the Measure-Object to show the count.  
```
PS E:\> $indicators | where-object {(get-date $_.creationTimeDateTimeUtc) -LT (Get-Date "2021-11-17")} | Measure-Object



Count    : 9350
Average  :
Sum      :
Maximum  :
Minimum  :
Property :
```
And as a sanity check, I'm also checking the count of objects using an older date and expecting an lower number of results returned.
```
PS E:\> ($indicators | where-object {(get-date $_.creationTimeDateTimeUtc) -LT (Get-Date "2021-11-05")} | Measure-Object).Count

1421
```
And this information is also useful for my awareness as I'm looking to remove older items from the Microsoft Endpoint Defender database
