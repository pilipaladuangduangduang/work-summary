# Java8新特性工作归纳

标签（空格分隔）： 工作总结

---

## Java8Lambda表达式

``` java
List<String> list = new ArrayList<String>();

list.add("a");
list.add("d");
list.add("c");

// 使用Java8Lambda之前，如果要进行List排序需要这样子
Collections.sort(list, new Comparator<String>() {
    @Override
    public int compare(String s1, String s2) {
        return s1.compareTo(s2);
    }
});

// 使用Java8Lambda
Collections.sort(list, (String s1, String s2) ->{
    return s1.compareTo(s2);
});

// 更加的简洁，编译器会进行类型推导
Collections.sort(list, (s1, s2) -> s1.compareTo(s2));
```

## Java8中使用Optional

``` java
Optional<String> o = Optional.of("1");
// 值不为null就简单的处理
o.ifPresent(s -> System.out.print(s));

List<String> list = new ArrayList<>();
// 如果值不为null就存储到List中
o.ifPresent(list::add);

// 相对返回结果进行处理就需要用到map
Optional<Boolean> r = o.map(list::add);
```

## Java8中的时间的API

### LocalDate常见用法

``` java
LocalDate ld = LocalDate.now();

LocalDate ld0 = LocalDate.of(2017, 4, 21);

LocalDate ld1 = LocalDate.of(2017, Month.JULY, 21);

/** 从1970-01-01算起的第xxx天：1999-12-25 */
LocalDate ld2 = LocalDate.ofEpochDay(365 * 30);

/** 指定年的第xxx天：2017-12-31 */
LocalDate ld3 = LocalDate.ofYearDay(2017,365);

/** 往当前时间依次添加一天、一个月、一个礼拜、一年 */
ld.plusDays(1);
ld.plusMonths(1);
ld.plusWeeks(1);
ld.plusYears(1);

/** 往当前时间依次减少一天、一个月、一个礼拜、一年 */
ld.minusDays(1);
ld.minusMonths(1);
ld.minusWeeks(1);
ld.minusYears(1);

/** 新增或者减少时间 */
LocalDate ld4 = ld.plus(100, ChronoUnit.DAYS);
LocalDate ld5 = ld.minus(100, ChronoUnit.DAYS);

/** 返回当前时间在当前月的第几天 */
int dm = ld.getDayOfMonth();
/** 返回当前时间在当前年的第几天 */
int dy = ld.getDayOfYear();
/** 返回当前时间是星期几 */
DayOfWeek dw = ld.getDayOfWeek();

/** 返回两个时间段之间的天数、月数、礼拜数或年数等等 */
long until = ld.until(LocalDate.of(2015, 6, 30), ChronoUnit.MONTHS);

/** 比较两个时间的前后 */
ld.isAfter(LocalDate.of(2015, 6, 30));
ld.isBefore(LocalDate.of(2015, 6, 30));
ld.isEqual(LocalDate.of(2015, 6, 30));
/** 判断是否为闰年 */
ld.isLeapYear();
```

### TemporalAdjuster时间调节器常见用法

``` java
/** 当前时间的后一个礼拜一 */
LocalDate ld0 = ld.with(TemporalAdjusters.next(DayOfWeek.MONDAY));

/** 当前时间的后一个礼拜一（包含当前时间） */
LocalDate ld1 = ld.with(TemporalAdjusters.nextOrSame(DayOfWeek.MONDAY));

/** 当前时间的前一个礼拜一 */
LocalDate ld2 = ld.with(TemporalAdjusters.previous(DayOfWeek.MONDAY));

/** 当前时间的前一个礼拜一（包含当前时间） */
LocalDate ld3 = ld.with(TemporalAdjusters.previousOrSame(DayOfWeek.MONDAY));

/** 当前月的第一个星期一 */
LocalDate ld4 = ld.with(TemporalAdjusters.dayOfWeekInMonth(1, DayOfWeek.MONDAY));

/** 当前月的最后一个星期一 */
LocalDate ld5 = ld.with(TemporalAdjusters.lastInMonth(DayOfWeek.MONDAY));

/** 当前月的第一天 */
LocalDate ld6 = ld.with(TemporalAdjusters.firstDayOfMonth());

/** 下个月的第一天 */
LocalDate ld7 = ld.with(TemporalAdjusters.firstDayOfNextMonth());

/** 明年的第一天 */
LocalDate ld8 = ld.with(TemporalAdjusters.firstDayOfNextYear());

/** 当前月的最后一天 */
LocalDate ld9 = ld.with(TemporalAdjusters.lastDayOfMonth());

/** 今年的最后一天 */
LocalDate ld10 = ld.with(TemporalAdjusters.lastDayOfYear());
```

### 实现自己的TemporalAdjuster

``` java
TemporalAdjuster MYSELF_TIME_ADJUSTER = TemporalAdjusters.ofDateAdjuster(w -> {
    LocalDate ld = w;
    // handle ...
    return ld;
});
```

### LocalTime本地时间

LocalTime大部分API操作和LocalDate差不多，所以无需介绍~

### LocalDateTime日期和时间

大部分API操作和LocalDate也差不多，所以也无需介绍~

### ZonedDateTime带时区的时间

这个用到的场景不多，等用到的时候，再去学习~

### 日期时间的格式化

DateTimeFormatter

### LocalDate和Date之间的转换

## Java8杂项归纳

