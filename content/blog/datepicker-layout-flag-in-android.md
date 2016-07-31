+++
date = "2015-06-07T20:54:09+09:00"
draft = false
title = "[Android] DatePicker のレイアウトのフラグがカオスな件について"
tags = [
    "Android",
]

+++

## カオスな DatePicker のレイアウトのフラグ

|属性|値|API Level|
|---|---|---|
|`android:calendarViewShown`|`true` or `false`|11|
|`android:spinnersShown`|`true` or `false`|11|
|`android:datePickerMode`|`spinner` or `calendar`|21|

`datePickerMode`は未指定もしくは`spinner`がLollipop 未満の表示です。<br>
`calendar`が Lollipop からの表示になります。<br>

レイアウトの切り替えにフラグが3種類って嫌な予感しかしませんね。

## 各属性を設定した場合の表示の違い

`Theme.AppCompat`を指定した`FragmentActivity`のスクリーンショットです。<br>

||DatePickerPattern1|DatePickerPattern2|DatePickerPattern3|DatePickerPattern4|DatePickerPattern5|DatePickerPattern6|DatePickerPattern7|DatePickerPattern8|
|---|---|---|---|---|---|---|---|---|
|`android:datePickerMode`|spinner|spinner|spinner|spinner|calendar|calendar|calendar|calendar|
|`android:calendarViewShown`|false|false|true|true|false|false|true|true|
|`android:spinnersShown`|false|true|false|true|false|true|false|true|
|Screenshot|![DatePickerInFragmentActivity_Spinner_isCalendarViewShown_false_isSpinnersShown_false_Theme_AppCompat.png](https://github.com/wada811/Android-DatePicker-Sample/raw/master/screenshots/DatePickerInFragmentActivity_Spinner_isCalendarViewShown_false_isSpinnersShown_false_Theme_AppCompat.png)|![DatePickerInFragmentActivity_Spinner_isCalendarViewShown_false_isSpinnersShown_true_Theme_AppCompat.png](https://github.com/wada811/Android-DatePicker-Sample/raw/master/screenshots/DatePickerInFragmentActivity_Spinner_isCalendarViewShown_false_isSpinnersShown_true_Theme_AppCompat.png)|![DatePickerInFragmentActivity_Spinner_isCalendarViewShown_true_isSpinnersShown_false_Theme_AppCompat.png](https://github.com/wada811/Android-DatePicker-Sample/raw/master/screenshots/DatePickerInFragmentActivity_Spinner_isCalendarViewShown_true_isSpinnersShown_false_Theme_AppCompat.png)|![DatePickerInFragmentActivity_Spinner_isCalendarViewShown_true_isSpinnersShown_true_Theme_AppCompat.png](https://github.com/wada811/Android-DatePicker-Sample/raw/master/screenshots/DatePickerInFragmentActivity_Spinner_isCalendarViewShown_true_isSpinnersShown_true_Theme_AppCompat.png)|![DatePickerInFragmentActivity_Calendar_isCalendarViewShown_false_isSpinnersShown_false_Theme_AppCompat.png](https://github.com/wada811/Android-DatePicker-Sample/raw/master/screenshots/DatePickerInFragmentActivity_Calendar_isCalendarViewShown_false_isSpinnersShown_false_Theme_AppCompat.png)|![DatePickerInFragmentActivity_Calendar_isCalendarViewShown_false_isSpinnersShown_true_Theme_AppCompat.png](https://github.com/wada811/Android-DatePicker-Sample/raw/master/screenshots/DatePickerInFragmentActivity_Calendar_isCalendarViewShown_false_isSpinnersShown_true_Theme_AppCompat.png)|![DatePickerInFragmentActivity_Calendar_isCalendarViewShown_true_isSpinnersShown_false_Theme_AppCompat.png](https://github.com/wada811/Android-DatePicker-Sample/raw/master/screenshots/DatePickerInFragmentActivity_Calendar_isCalendarViewShown_true_isSpinnersShown_false_Theme_AppCompat.png)|![DatePickerInFragmentActivity_Calendar_isCalendarViewShown_true_isSpinnersShown_true_Theme_AppCompat.png](https://github.com/wada811/Android-DatePicker-Sample/raw/master/screenshots/DatePickerInFragmentActivity_Calendar_isCalendarViewShown_true_isSpinnersShown_true_Theme_AppCompat.png)|

DatePickerPattern1~4 は Lollipop 未満の表示です。<br>
DatePickerPattern5~8 は`android:datePickerMode="calendar"`が
`calendarViewShown`と`spinnersShown`より強いので全て Lollipop のカレンダー表示になっています。<br>

## どうしてこうなった

そもそも `bool` で定義すべきではない表示形式を
`calendarViewShown` と `spinnersShown` の2種類の `bool` で表してしまったことが全ての元凶です。
本来は3種類のレイアウトを2種類の `bool` で表現したため、4種類の状態が表現できるようになってしました。
そこに `calendarViewShown` と `spinnersShown` よりも強い `datePickerMode` が追加されカオスな状況になりました。

## どうすべきだったのか
`calendarViewShown`と`spinnersShown`の両方に`false`を指定した際に<br>
何も表示しない状態がある時点で怪しいと思わなければならないと思います。<br>
Lollipop 未満の時点で表示形式は以下のような`enum`で定義したほうが良かったと思います。
```
enum DatePickerMode {
    spinenr,
    calendar,
    spinner_and_calendar
}
```
そうすれば DatePickerPattern1 のような何も表示されない状態もなく、<br>
Lollipop で追加された表示形式も、テーマ次第で`calendar`などの表示を変えてしまうとか<br>
`material`とか何とかを`enum`の値に追加するだけで済んだと思います。<br>

## まとめ
怪しいフラグを増やしそうになったら抽象化して考えて`bool`が適切か考えましょう。

<blockquote class="twitter-tweet" data-partner="tweetdeck"><p lang="ja" dir="ltr">Android の DatePicker がクソとかそういう話はどうでもよくて、その bool 、本当に bool ですか？という話です</p>&mdash; あなたとReSharper (@wada811) <a href="https://twitter.com/wada811/status/608622501401223168">June 10, 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

## リポジトリ
[wada811/Android-DatePicker-Sample](https://github.com/wada811/Android-DatePicker-Sample)