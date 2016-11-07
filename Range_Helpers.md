Алгоритмы и адапторы, обеспечиваемые Boost.Range основаны на шаблонах. Вам не придется изменять контейнер для диапозона, чтобы передать его алгоритму или адаптору. Тем не менее, Boost.Range определяет немного классов диапозонов, а `boost::iterator_range` самый важный из них. `boost::iterator_range` необходим, т.к. адапторы и некоторые алгоритмы возвращают диапозоны, у которых должен быть тип. В дополнении есть вспомогательные функции, которые создают диапозоны, чьи итераторы содержат все те данные, необходимые для итерации. Итераторы из таких диапозонов не ссылаются на контейнер или другую структуру данных.  Класс вроде `boost::iterator_range` здесь также используется.

<a name="ex.range_07"/>
Пример 30.7. Фильтруем диапозон с `boost::adaptors::filter()`
```c++
#include <boost/range/algorithm.hpp>
#include <boost/range/irange.hpp>
#include <iostream>

int main()
{
  boost::integer_range<int> ir = boost::irange(0, 3);
  boost::copy(ir, std::ostream_iterator<int>{std::cout, ","});
}
```

[Пример 30.7](#ex.range_07) использует функцию `boost::irange()`. Эта функция создает диапозон для целочисленных значений (тип int) без необходимости использовать контейнер или другую структуру данных. Вам только нужно передать верхнюю и нижнюю границу в `boost::irange()` с исключительной верхней гранью.

`boost::irange()` возвращает диапозон типа `boost::integer_range`. Этот класс наследован от `boost::iterator_range`. `boost::iterator_range` - шаблонный класс, принимающий в качестве своего единственного параметра шаблона int. Итератор, использованный с `boost::irange()` тесно связан с этой функцией и деталь реализации. is tightly coupled to that function and is an implementation detail. Таким образом, тип итератора не обязательно передавать как шаблонный параметр в `boost::iterator_range`.  Тем не менее, `boost::integer_range` принимает только целочисленный тип, отчего он проще в использовании. 

[Пример 30.7](#ex.range_07) выведет `0,1,2`.

<a name="ex.range_08"/>
Пример 30.8. Создаем диапозон в поток ввода с `boost::istream_range()`
```c++
#include <boost/range/algorithm.hpp>
#include <boost/range/istream_range.hpp>
#include <iterator>
#include <iostream>

int main()
{
  boost::iterator_range<std::istream_iterator<int>> ir =
    boost::istream_range<int>(std::cin);
  boost::copy(ir, std::ostream_iterator<int>{std::cout, "\n"});
}
```

[Пример 30.8](#ex.range_08) знакомит вас с `boost::istream_range()`, которая создает диапозон для потока ввода. Функция возвращает диапозон как `boost::iterator_range`. Это означает, что тип итератора должен быть передан как шаблонный параметр.

Когда вы запустите [Пример 30.8](#ex.range_08), напечатете число и нажмете **Enter**, число выведется на следующей строчке. Если введете другое число и нажмете **Enter**, выведется то число. Диапозон, возвращаемый `boost::istream_range()` позволяет `boost::copy()` перебирать все введеные числа и выводить их в **std::cout**.

Вы можете прекратить работу программы в любой момент, нажав **Ctrl+C**.

Кроме `boost::iterator_range`, Boost.Range обеспечивает класс `boost::sub_range`, который наследован от `boost::iterator_range`. `boost::sub_range` шаблон по типу `boost::iterator_range`. Однако `boost::sub_range` вместо типа итератора принимает тип диапозона как шаблоный параметр. Такой подход может упростить использование.

<a name="ex.range_09"/>
Пример 30.9. Еще проще создание диапозонов с `boost::sub_range()`
```c++
#include <boost/range/algorithm.hpp>
#include <boost/range/iterator_range.hpp>
#include <boost/range/sub_range.hpp>
#include <array>
#include <iterator>
#include <iostream>

int main()
{
  std::array<int, 6> a{{0, 1, 2, 3, 4, 5}};
  boost::iterator_range<std::array<int, 6>::iterator> r1 =
    boost::random_shuffle(a);
  boost::sub_range<std::array<int, 6>> r2 =
    boost::random_shuffle(r1);
  boost::copy(r2, std::ostream_iterator<int>{std::cout, ","});
}
```
Немного алгоритмов Boost.Range возвращают диапозон – например, `boost::random_shuffle()`. Этот алгоритм непосредственно изменяет диапозон, переданный ему по ссылке и возвращает измененный диапозон. В [Примере 30.9](#ex.range_09), `boost::random_shuffle()` вызывается дважды, так что массив случайно перемешан дважды.

Как первое возвращаемое значение, пример использует `boost::iterator_range`; как второе `boost::sub_range`. Использование обоих этих классов отличается только в параметре шаблона. Не только `boost::sub_range` может быть инстанциирован куда проще, но он также обеспечивает определение типа `const_iterator`.