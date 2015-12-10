# GasL - Google Apps Script Logging-framework

GasL is a logging framework for Google Apps Script(GAS). It provides a unix syslog like simple way for the GAS programs to log messages to Spreadsheet, LogEntries, RESTFUL API and Logger of GAS.

Github: https://github.com/zixia/gasl

In order to write to different log destinations, GasL comes with a components named Printer, which uses DI(Dependency Injection) to provide the write capacity. Under the hood, each Printer components is just a function with param for message, and write them out.

The following samples is runable. May be you want to copy/paste it inside script editor, then try to execute.

## Basic Sample:

This sample use Logger for output. "Ctrl + Enter" to get your logs.

```javascript
var gasLogLib='https://raw.githubusercontent.com/zixia/gasl/master/gas-log.js'
var GasLog = eval(UrlFetchApp.fetch(gasLogLib).getContentText())
var log = new GasLog()
    
log('Hello, %s!', 'World')
```

## Sample that use a Google Spreadsheet to output:

This sample use a spreadsheet to output. You can open the URL: https://docs.google.com/spreadsheets/d/1_KRAtoDz2Pdcj9IPZI007I_gMzRyfmXf7gicgxVwYJc/edit#gid=0 , to see your log output.

```javascript
var gasLogLib='https://raw.githubusercontent.com/zixia/gasl/master/gas-log.js'
var GasLog = eval(UrlFetchApp.fetch(gasLogLib).getContentText())

var printer = new GasLog.Printer.Spreadsheet({
  url: 'https://docs.google.com/spreadsheets/d/1_KRAtoDz2Pdcj9IPZI007I_gMzRyfmXf7gicgxVwYJc/edit#gid=0'
  , sheetName: 'Logs'
  , clear: true
  , scroll: 'UP'
})
  
var log = new GasLog({
  printer: printer
  , logLevel: 'INFO'
})

log(log.INFO, 'Hello, %s!', 'Spreadsheet')
```

GasL is designed for running javascript on Google Apps Script environment only.

## How to Logging

There's a very simple example at https://github.com/zixia/gasl/blob/master/gasl-demo.js , which is the demo sample code of GasL.

### `GasLog`: GasL Module

We use eval to get GasLog module in our code:

```javascript
var gasLogLib='https://raw.githubusercontent.com/zixia/gasl/master/gas-log.js'
var GasLog = eval(UrlFetchApp.fetch(gasLogLib).getContentText())
```
GasLog is the main module of GasL.

### `Printer`: compoment for output DI

GasLog.Printer is used by injector to enable output to different destination of GasT.

It has 2 Printer: 
1. `GasLog.Printer.Logger`
1. `GasLog.Printer.Spreadsheet`

#### `GasLog.Printer.Logger`

Logger is the default Printer of GasLog. 

It use the Logger.log of Google Apps Script to output.

No need to set any options.

```javascript
var gasLogLib='https://raw.githubusercontent.com/zixia/gasl/master/gas-log.js'
var GasLog = eval(UrlFetchApp.fetch(gasLogLib).getContentText())

var loggerPrinter = new GasLog.Printer.Logger()

var log = new GasLog({
  printer: loggerPrinter
})
```

#### `GasLog.Printer.Spreadsheet`

Spreadsheet is for print to a spreadsheet.

It can use a Spreadsheet URL or ID to specify a sheet, then log to that sheet.

You need to specify the param of options:
1. url(string): Google Spreadsheet url. You must have the write permisstion for that spreadsheet. (one of url/id must be set)
1. id(string): GoogleSpreadsheet id. You must have the write permisstion for that spreadsheet. (one of url/id must be set)
1. sheetName(string): Tab name of the output sheet. Will be created if not exist.
1. clear(bool): true for clear the sheet before output. default false.
1. scroll(string): 'UP' for insert new log to the top. default 'DOWN'.

```javascript
var gasLogLib='https://raw.githubusercontent.com/zixia/gasl/master/gas-log.js'
var GasLog = eval(UrlFetchApp.fetch(gasLogLib).getContentText())

var sheetPrinter = new GasLog.Printer.Spreadsheet({
  url: 'https://docs.google.com/spreadsheets/d/1_KRAtoDz2Pdcj9IPZI007I_gMzRyfmXf7gicgxVwYJc/edit#gid=0'
  , sheetName: 'Logs'
  , clear: true
  , scroll: 'UP'
})
  
var log = new GasLog({
  printer: sheetPrinter
  , logLevel: 'INFO'
})
```

#### `log(message)`: the simplest version

Just use it as look:

```javascript
log('Hello, World!')
```

Then you message will be logged.

#### `log(priority, format, ...)`: the unix syslog like function

log the message by `format` of `priority`.

1. `format`: only support '%s' because javascript only has this.
1. `priority`: should be one of the follow.
  1. `log.EMERG`
  1. `log.ALERT`
  1. `log.CRIT`
  1. `log.ERR`
  1. `log.WARNING`
  1. `log.NOTICE`
  1. `log.INFO`
  1. `log.DEBUG`

#### `log.setPriority(priority)`

Set priority for this log.

```javascript
log.setPriority(log.ERR)
```

#### `log.getPriority()`

Get priority for this log.

```javascript
var priority = log.getPriority()
```

### Screen Snapshoot
![GasLog for Google Apps Script](https://raw.githubusercontent.com/zixia/gasl/master/gas-log.png)

A online version of google spreadsheet bounded with GasL google apps scripts can be found here: 
* Spreadsheet - https://docs.google.com/spreadsheets/d/19M2DY3hunU6tDQFX5buJmZ_f3E8VFmlqAtodyC-J8Ag/edit#gid=1761137024
* Script editor - https://script.google.com/a/zixia.net/macros/d/Mta4oea1VMIugfSGRo4QrAnKRT9d30hqB/edit?uiv=2&mid=ACjPJvGt4gnXjJwXnToB0jIMEbSvqKUF6vH-uq-m59SqnjXqTQ03NDn_khlNE6ha_mPnrOAYEnyFk80nHYmt_hppO3AgDkO_vVLrYJXzcPPagwRromd0znfLreNFAu4p0rYTC-Jlo-sAKOM

## Using GasL in Google Apps Script

Install GasL is very easy: just copy/paste the following javascript code to your Code.gs file, then you are ready to use GasL.

```javascript
var gasLogLib='https://raw.githubusercontent.com/zixia/gasl/master/gas-log.js'
var GasLog=eval(UrlFetchApp.fetch(gasLogLib).getContentText())
```

Then you are ready to use `var log = new GasLog(...)`.

## Support

The GasL source code repository is hosted on GitHub. There you can file bugs on the issue tracker or submit tested pull requests for review. ( https://github.com/zixia/gasl/issues )

For real-world examples from open-source projects using GasL, see Projects Using TasL on the wiki. ( https://github.com/zixia/gasl/wiki )


## Version history

### 0.1.0 (December 10, 2015)
* Initial public release.

-------------------------------------------
© 2015 Zhuohuan LI. GasL is released under an MIT-style license; see LICENSE for details.
