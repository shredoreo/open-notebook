 Guava提供了一个新集合类型Multiset，它可以多次添加相等的元素，且和元素顺序无关。Multiset继承于JDK的Cllection接口，而不是Set接口。它和set最大的区别就是

它可以对相同元素做一个计数的功能，普通的 Set 就像这样 :[car, ship, bike]，而 Multiset 会是这样 : [car x 2, ship x 6, bike x 3]Multiset有一个有用的功能，就是跟踪每种对象的数量，所以你可以用来进行数字统计。每存放一个相同元素，那么该元素的count就加1。

譬如一个 List 里面有各种字符串，然后你要统计每个字符串在 List 里面出现的次数，这个用Multiset就能够快速实现。

