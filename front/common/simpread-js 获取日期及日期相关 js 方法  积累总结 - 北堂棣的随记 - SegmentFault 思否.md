> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 https://segmentfault.com/a/1190000015381362

常用如下：

```
    var date = new Date();
    var year = date.getFullYear();
    var month = date.getMonth();
    var nowDate = date.getDate();
    var day = date.getDay();

```

更多请点击 [JavaScript 标准库 Date](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 或相关参考的第一篇 [Js 获取当前日期时间及其它操作](http://www.cnblogs.com/carekee/articles/1678041.html)。

格式化日期
-----

### 1、yyyy-MM-dd

```
function formatDate(date) {
    var myyear = date.getFullYear();
    var mymonth = date.getMonth() + 1;
    var myweekday = date.getDate();
 
    if (mymonth < 10) {
        mymonth = "0" + mymonth;
    }
    if (myweekday < 10) {
        myweekday = "0" + myweekday;
    }
    return (myyear + "-" + mymonth + "-" + myweekday);
}

var date = new Date();


formatDate(date);


```

或者 [js 标准时间格式化](https://segmentfault.com/q/1010000015380440)中 @joy 钰的回答：

```
function formatDate(dateArg) {
    const date = new Date(dateArg);
    const year = date.getFullYear();
    const month = date.getMonth() + 1;
    const day = date.getDate();
    const formatMonth = month < 10 ? `0${month}` : month;
    const formatDay = day < 10 ? `0${day}` : day;

    return `${year}-${formatMonth}-${formatDay}`
}


```

### 2、xx 年 xx 月 xx 日 xx 时 xx 分

```
function todayTime() {
        var date = new Date();
        var curYear = date.getFullYear();
        var curMonth = date.getMonth() + 1;
        var curDate = date.getDate();
        if(curMonth<10){
                curMonth = '0' + curMonth;
        }
        if(curDate<10){
                curDate = '0' + curDate;
        }    
        var curHours = date.getHours();
        var curMinutes = date.getMinutes();
        var curtime = curYear + ' 年 ' + curMonth + ' 月 ' + curDate +' 日' + curHours + '时 ' + curMinutes + '分 ';
        return curtime;
}

```

### 3、英文格式日期

```
function todayTimeEn() {
        var dt = new Date();
        var m = new Array("January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December");
        mn = dt.getMonth();
        dn = dt.getDate();
        if(dn<10){
                dn = '0' + dn;
        }
        var curtime = m[mn] + " " + dn + ", " + dt.getFullYear();
        return curtime;
}


```

### 4、 自定义任意格式

```
Date.prototype.Format = function (fmt) {
    var o = {
        "M+": this.getMonth() + 1, 
        "d+": this.getDate(), 
        "h+": this.getHours(), 
        "m+": this.getMinutes(), 
        "s+": this.getSeconds(), 
        "q+": Math.floor((this.getMonth() + 3) / 3), 
        "S": this.getMilliseconds()
        
    };
    if (/(y+)/.test(fmt))
        fmt = fmt.replace(RegExp.$1, (this.getFullYear() + "")
            .substr(4 - RegExp.$1.length));
    for (var k in o)
        if (new RegExp("(" + k + ")").test(fmt))
            fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (o[k]) :
                (("00" + o[k]).substr(("" + o[k]).length)));
    return fmt;
   }

```

使用方法：

```
new Date().Format('yyyy-MM-dd hh:mm')
//console.log打印结果： "2019-08-22 16:42"

new Date().Format('yyyy.MM.dd hh:mm')
//console.log打印结果： "2019.08.22 16:44"

new Date().Format('yyyy-MM-dd')
//console.log打印结果："2019-08-22"

```

周、月 相关的日期方法
-----------

### 1、一周 、本周

```
function setDate() {
            var now = new Date();
            
            var millisecond = 1000 * 60 * 60 * 24;
            var end= new Date(now.getTime() - (7 * millisecond));
 
            var beginDate = formatDate(now);
            var endDate = formatDate(end);
            console.log("beginDate："+beginDate);
            console.log("endDate ："+endDate );
 }
 

function weekDate() {
        var date = new Date();
        var year = date.getFullYear();
        var month = date.getMonth();
        var nowDate = date.getDate();
        var day = date.getDay();
        var beginDate = new Date(year, month, nowDate - day + 1);
        var endDate = new Date(year, month, nowDate + (6 - day) + 1);
        beginDate = formatDate(beginDate);
        endDate = formatDate(endDate);
        console.log("beginDate："+beginDate);
        console.log("endDate ："+endDate );
    }
 

```

### 2、 月

```
    //本月日期
    function monthDate() {
        var date = new Date();
        var year = date.getFullYear();
        var month = date.getMonth() + 1;
        if (month < 10) {
            month = "0" + month;
        }
        var day = getDaysInOneMonth(year, month);
        var beginDate = year + "-" + month + "-01";
        var endDate = year + "-" + month + "-" + day;
        console.log("beginDate："+beginDate);
        console.log("endDate ："+endDate );
    }
 
    //获取某月天数
    function getDaysInOneMonth(year, month) {
        month = parseInt(month, 10);
        var d = new Date(year, month, 0);
        return d.getDate();
    }

```

### 3、获取前天、昨天、今天、明天、后天的时间

```
function GetDateStr(AddDayCount) {
    var dd = new Date();
    dd.setDate(dd.getDate()+AddDayCount);
    var y = dd.getFullYear();
    var m = dd.getMonth()+1;
    var d = dd.getDate();
    return y+"-"+m+"-"+d;
}
console.log("前天："+GetDateStr(-2));
console.log("昨天："+GetDateStr(-1));
console.log("今天："+GetDateStr(0));
console.log("明天："+GetDateStr(1));
console.log("后天："+GetDateStr(2));
console.log("大后天："+GetDateStr(3));

```

上述方法，稍加修改，即可完成如下：

### 4、获取某个日期 date 的前天、昨天、今天、明天、后天的日期

```
function GetDateCountStr(date, AddDayCount) {
    let dd = new Date(date);
    dd.setDate(dd.getDate() + AddDayCount);
    let year = dd.getFullYear();
    let month = dd.getMonth() + 1;
    let day = dd.getDate();

    if (month < 10) {
        month = '0' + month;
    }
    if (day < 10) {
        day = '0' + day;
    }
    return (year + '-' + month + '-' + day);
}

```

使用：  
例如  
date 的前天 GetDateCountStr(date, -2)  
date 的昨天 GetDateCountStr(date, -1)  
date 的今天 GetDateCountStr(date,0)  
date 的明天 GetDateCountStr(date,1)  
date 的后天 GetDateCountStr(date,2)

### 5、获取起始时间算起第 n 周的日期

```
function weeks_enddate(startdate,n){
    var date = new Date(startdate);
    date.setTime(date.getTime() + 3600 * 1000 * 24 * 7 * n);
    return formatDate(date);
}


var startdate = '2018-06-27';
var week_num = 2;
var enddate = weeks_enddate(startdate,week_num);
console.log("enddate ："+enddate );


```

上述方法，稍加修改，即可完成如下：

### 以周一为一周的第一天算，获取以开始日期所在周为第一周算起的第 n 周周日的日期

```
function weeks_enddate(startdate,n){
    var date = new Date(startdate);
    var year = date.getFullYear();
    var month = date.getMonth();
    var nowDate = date.getDate();
    var day = date.getDay();
    if(day > 0){
        date = new Date(year, month, nowDate + (6 - day) + 1);
    }    
    date.setTime(date.getTime() + 3600 * 1000 * 24 * 7 * (n-1));
    return formatDate(date);
}


var startdate = '2018-08-10'; 
var n = 3;
weeks_enddate(startdate,n);


var startdate = '2018-08-10'; 
var n = 0;
weeks_enddate(startdate,n);


var startdate = '2018-08-10'; 
var n = -1;
weeks_enddate(startdate,n);

```

### 利用上述方法，灵活运用，即可获任意日期所在周的一周的日期

```
function weekyCalendar(date) {
    var dateFlag = weeksEndDate(date, 1); 
    if (dateFlag == date) {
        
        var daySun = GetDateCountStr(date, 0);
        var daySat = GetDateCountStr(date, 1);
        var dayFri = GetDateCountStr(date, 2);
        var dayThur = GetDateCountStr(date, 3);
        var dayWed = GetDateCountStr(date, 4);
        var dayTues = GetDateCountStr(date, 5);
        var dayMon = GetDateCountStr(date, 6);
        var days = [daySun, daySat, dayFri, dayThur, dayWed, dayTues, dayMon];
    } else {
        
        var daySun = GetDateCountStr(dateFlag, -7);
        var daySat = GetDateCountStr(dateFlag, -6);
        var dayFri = GetDateCountStr(dateFlag, -5);
        var dayThur = GetDateCountStr(dateFlag, -4);
        var dayWed = GetDateCountStr(dateFlag, -3);
        var dayTues = GetDateCountStr(dateFlag, -2);
        var dayMon = GetDateCountStr(dateFlag, -1);
        var days = [daySun, daySat, dayFri, dayThur, dayWed, dayTues, dayMon];
    }
    return days;
    
}

```

示例：

```
var dates = weekyCalendar('2019-08-30');
console.log(dates);
//(7) ["2019-08-25", "2019-08-26", "2019-08-27", "2019-08-28", "2019-08-29", "2019-08-30", "2019-08-31"]

var dates = weekyCalendar('2019-08-25');
console.log(dates);
//(7) ["2019-08-25", "2019-08-26", "2019-08-27", "2019-08-28", "2019-08-29", "2019-08-30", "2019-08-31"]

```

相关参考
----

[Js 获取当前日期时间及其它操作](http://www.cnblogs.com/carekee/articles/1678041.html)  
[js 将日期转换为英文格式](http://blog.163.com/pepsl@126/blog/static/5439330820121911458135/)  
[js 获取前天、昨天、今天、明天、后天的时间](http://www.cnblogs.com/gengaixue/archive/2011/07/05/2098299.html)