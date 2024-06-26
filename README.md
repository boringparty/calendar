[![Movies](https://github.com/boringparty/calendar/actions/workflows/cal-movies.yaml/badge.svg)](https://github.com/boringparty/calendar/actions/workflows/cal-movies.yaml)
[![TV](https://github.com/boringparty/calendar/actions/workflows/cal-tv.yaml/badge.svg)](https://github.com/boringparty/calendar/actions/workflows/cal-tv.yaml)
[![Sports](https://github.com/boringparty/calendar/actions/workflows/cal-sports.yaml/badge.svg)](https://github.com/boringparty/calendar/actions/workflows/cal-sports.yaml)

# Calendar
These are some calendars that are generated by a Google Sheet. Here's the workflow:

The sheet looks like this 

Day|Date|Name
---|:---:|---
Sat|2024-03-02|Formula1 - Bahrain
Sat|2024-03-02|Formula2 - Bahrain
Sat|2024-03-02|Formula3 - Bahrain
Sat|2024-03-09|Formula1 - Saudi Arabia
Sat|2024-03-09|Formula2 - Saudi Arabian
Sun|2024-03-10|Indycar - Florida
Sun|2024-03-10|MotoGP - Qatar
Sun|2024-03-10|Moto2 - Qatar
Sun|2024-03-10|Moto3 - Qatar

This is A, B, C --- then in D I have this absolutely overkill formula

```
=ARRAYFORMULA(
  VSTACK(
   JOIN(CHAR(13),
    "BEGIN:VCALENDAR",
    "VERSION:2.0",
    "PRODID:-// //EN",
    "CALSCALE:GREGORIAN",
    "X-WR-CALNAME;VALUE=TEXT:Sports"),
   IF(ISBLANK(C2:C),,
    MAP(
     C2:C,
     B2:B,
     D2:D,
     LAMBDA(
      a,b,g,
      TEXTJOIN(CHAR(13),1,
       "BEGIN:VEVENT",
       "SUMMARY:"&a,
       "DTSTAMP:"&TEXT(b,"YYYYMMDD")&"T"&TEXT(b-INT(b),"hhmm"),
       "DESCRIPTION:"&g,
       "DTSTART;VALUE=DATE:"&TEXT(b,"YYYYMMDD"),
       "DTEND;VALUE=DATE:"&TEXT(b,"YYYYMMDD"),
       "END:VEVENT"))))))       
```

which generates...

```
BEGIN:VCALENDAR
VERSION:2.0
PRODID:-// //EN
CALSCALE:GREGORIAN
X-WR-CALNAME;VALUE=TEXT:Sports
BEGIN:VEVENT
SUMMARY:Formula1 - Bahrain
DTSTAMP:20240302T1900
DESCRIPTION:
DTSTART;VALUE=DATE:20240302
DTEND;VALUE=DATE:20240302
END:VEVENT
TRIGGER:-PT120M
BEGIN:VEVENT
SUMMARY:Formula2 - Bahrain
DTSTAMP:20240302T0000
DESCRIPTION:
DTSTART;VALUE=DATE:20240302
DTEND;VALUE=DATE:20240302
END:VEVENT
...
```

Next up, publish the workbook and add `&single=true&output=tsv&range=D1:D` to the end of the URL. This will output the one column as a tab-separated value, which is really just plaintext since we're only showing the one column.

Last, we feed it through the flow and have it spit out an .ics file that can be used with any calendar app as a subscription. 
