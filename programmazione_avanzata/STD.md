https://en.cppreference.com/w/cpp/standard_library

# string
https://cplusplus.com/reference/string/string/
```cpp
#include <string>
using namespace std;
int main() {
	string str("string");
	string str(number,character); // repeat same char
	int size = str.length();
	string r = str.substr(size_t pos, size_t len);
	// pos: position of the first character to be copied
	// len: length of the sub-string
	size_t find (const string& str, size_t pos = 0);
	// returns the index of the first occurrence of the sub-string
}
```

## regex

```cpp
#include <iostream>
#include <string>
#include <regex>
using namespace std;
int main() {
	string s = "HELLOworld";
	regex r("(HELLO)(.*)"); // HELLO followed by any character
	if ( regex_match(s, r) ) { cout << "match" << endl; }
	if ( regex_match(s.begin(), s.end(), r) ) { 
		cout << "match a range" << endl;
	}
	string s1 = "HELLOworld"
		"and HELLOyou too";
	regex r1("HELLO[a-zA-Z]+");
	smatch m;
	regex_search(s1, m, r1);
	for (auto x : m) cout << x << " "; cout << endl;
	// print "HELLOworldand"
	
	cout << regex_replace("hey friend!", r, "FRIEND") << endl;
	// print "hey FRIEND"
}
```

TODO: regex syntax
# containers
1. **Sequence Containers**: dequeue, list, and vector
2. **Associative Containers**: (multi)set, (multi)map, hash_(multi)set, hash_(multi)map
3. **Container Adapters**: queue, priority queue, and stack
4. **Unordered Associative containers**: unordered_(multi)set, unordered_(multi)map


all of them have standard iterators:
`begin()`, `end()`, `rbegin()`, `rend()`, `crbegin()`, `crend()`, `cbegin()`, `cend()`
and basic functions:
- `c.size()` returns the number of elements
- `c.max_size()` returns the maximum number of elements that can hold
- `c.empty()` returns whether is empty
- `c.clear()` remove all elements

## vector
```cpp
#include <vector>
#include <iostream>
using namespace std;
int main() {
	vector<char> v( {'a','b','c','d'} );
	vector<int> v2(4, 10);    // four int of value 10
	vector<char> v3(v);       // deep copy
	v2.resize(100);           // set capacitiy to n elements
	int elements = v2.size(); // 100
	char arbitrary = v.at(2);
	char first = v.front();
	char last = v.back();
	v.push_back('e'); // insert to the end
	v.pop_back();     // remove the last element
	v.clear();        // remove all elements
	bool is_empty = v.empty();
	
	// iterators
	vector<char>::iterator iter_to_first_e = v3.begin();
	vector<char>::iterator iter_to_last_e = v3.end()-1;
	cout << *iter_to_first_e << endl; // deference
}
```
## set

|op|set|unordered_set|
|-|-|-|
|ordering|increasing order of keys|no ordering|
|implementation|self balancing BST|hash table|
|search|Ω(log(n))|O(1) average, <br>O(n) worst case|
|insertion|Ω(log(n))+rebalance|"|
|deletion|"|"|

use set when
- We need ordered data
- We would have to print/access the data (in sorted order)
- We need predecessor/successor of elements
- See [advantages of BST over Hash Table](https://www.geeksforgeeks.org/advantages-of-bst-over-hash-table/) for more cases
use unordered_set when
- no ordering is required
- you need single element access i.e. no traversal

```cpp
#include<iostream>
#include<set>
#include<unordered_set>
using namespace std;
int main() {
	set<int> oset = {6, 9, 5, 1};
	oset.insert(5);
	set<int, greater<int> > desc = {6, 9, 5, 1};
	oset.erase(50); // return true if the element was present
	for(int e: oset) cout << e << " "; cout << endl; // 1 5 6 9
	for(int e: desc) cout << e << " "; cout << endl; // 9 6 5 1

	if ( oset.find(9) != oset.end() ) cout << "found" << endl;
	if ( oset == (set<int>){1,5,9,1,6} ) cout << "equals" << endl;
}
```
## map

have the same pros/cons than (unordered_)set because the inner implementation is the same

```cpp
#include <iostream>
#include <map> // map and multimap
#include <unordered_map> // unordered_map and unordered_multimap
using namespace std;
struct cmp {
	bool operator()(const string& a, const string& b) const {
		return a.length() < b.length();
	}
};
int main() {
	map<int,string> omap;
	omap[1] = "one";
	omap.insert(pair(2,"two"));
	omap.insert({4,"four"});
	
	omap[1] = "eleven"; // if the key already exists, substitute it
	
	// only insert if the key doesn't exist
	omap.emplace(1,"one");
	omap.emplace(3,"three");
	
	// get value by key, if not present return the default value
	cout << omap[4] << endl;
	// get pair by key, if not present return the .end() iterator
	map<int, string>::iterator iter = omap.find(4);
	if ( iter != omap.end() ) cout << (*iter).second << endl;
	
	for (auto _pair : omap)
		cout << _pair.first << ' ' << _pair.second << endl;
	
	omap.erase(3);
	// remove pair by key, return (bool) if was present
	
	// in multimaps keys are not unique
	multimap<int, char> mmap({{4,'a'},{6,'b'},{4,'c'}});
	mmap.count(4); // return how many key of value '4' exists
	
	// string are ordered lexicographically
	// if you want to change it you need to override the comparator
	map<string,int, cmp> cmp_map({{"aaa",1},{"bb",2},{"c",3}});
	for (auto [key, val] : cmp_map)
		cout << key << ':' << val << endl;
}
```

## queue

A **priority queue** is specifically designed such that the first element of the queue is either the greatest or the smallest of all elements in the queue, and elements are in non-increasing or non-decreasing order.
But we can also change it to the smallest element at the top.

```cpp
#include <iostream>
#include <queue>
#include <vector>
using namespace std;
int main() {
	vector<int> vec( { 25 , 35 , 5 } ) ;
	priority_queue <int> pq( vec.begin() , vec.end() );
	pq.push(15);
	while (! pq.empty() ) {
		int head = pq.top(); // return the first element
		cout << head << " ";
		pq.pop(); // remove the first element
	}
	cout << endl; // print 35 25 15 5
	
	// inverted order
	priority_queue<int, vector<int>, greater<int> > min_pq;
	min_pq.push(8);
	min_pq.push(2);
	min_pq.push(4);
	while (! min_pq.empty() ) {
		cout << min_pq.top() << " ";
		min_pq.pop();
	}
	cout << endl; // print 2 4 8
	
	// FIFO queue
	queue<string> q;
	q.push("cat");
	q.push("dog");
	q.push("bird");
	while(!q.empty()) {
		// equals to top() for priority queues
		cout << q.front() << " ";
		q.pop();
	}
	cout << endl; // print cat dog bird
	return 0;
}
```
## stack
Stacks are a type of container adaptors with LIFO (Last In First Out) type of working, where a new element is added at one end (top) and an element is removed from that end only.

```cpp
#include <iostream>
#include <stack>
using namespace std;
int main() {
	stack<int> s({21,22});
	s.push(24);
	s.push(25);
	while (!s.empty()) {
		cout << s.top() <<" "; // return the most recent entered value
		s.pop(); // remove the most recent entered element
	}
	cout << endl; // print 25 24 22 21
}
```

# algorithms
https://www.cplusplus.com/reference/algorithm/
```cpp
#include <iostream>
#include <algorithm>
#include <chrono>
#include <vector>
using namespace std;
int main() {
	int a = 2, b = 3;
	swap(a, b); // a and b of the same type
	
	auto duration = chrono::system_clock::now().time_since_epoch();
	auto millis = chrono::duration_cast<chrono::milliseconds>
	(duration).count();
	// millis since epoch
	
	vector<int> v{ 1, 5, 8, 9, 6, 7, 3, 4, 2, 0 };
	sort(v.begin(), v.end());
	for (auto x : v) cout << x << " "; cout << endl;
	
	bool found = find(v.begin(), v.end(), 6) != vec.end();
}
```

# iterators
Iterators are a means of navigating and operating on a sequence of elements and are a generalized extension of pointers.
Conceptually it is important to remember that iterators are positions, not elements.
https://cplusplus.com/reference/iterator/
```cpp
#include <iostream>
#include <vector>
#include <cassert>
using namespace std;
int main() {
	vector<int> v( {1,2,3,4,5} );
	auto iter = v.begin();
	// navigating forward
	++iter;
	advance(iter, 1);
	iter = next(iter);
	iter = next(iter, 1);
	// navigating backward
	advance(iter, -1);
	iter = prev(iter, 1);
	auto dist = distance(v.begin(), iter); // 2
	for (auto i = v.begin(); i != v.end(); ++i)
	cout << *i << " "; cout << endl;
	// reverse iterators
	assert ( *(v.rend()-1) == *v.begin() == *v.rend().base() );
	assert ( *(v.rbegin()+1) == *v.end() == *v.rbegin().base() );
	// read only
	const vector<int>::const_iterator ci = v.begin();
}
```

# tuple
A tuple is an object that can hold a number of elements of different data types.

a **pair** is a tuple of exactly two elements
```cpp
#include <iostream>
#include <utility>
using namespace std;
int main() {
	pair<int, char> p{10, 'a'};
	p = make_pair(2, 'b');
	p = {3, 'c'};
	auto p1(p);
	p1.second = 'f';
	cout << p.first << " " << p.second << endl;
	cout << p1.first << " " << p1.second << endl;
	
	bool equals = (p == p1);
	bool min = (p < p1);
	// logical operators ( >=, <= ) only comparing the first value
	return 0;
}
```

a **tuple** can have an arbitrary number of elements, but decided at compile time
```cpp
#include <iostream>
#include <tuple> // include the type `pair` too
#include <sstream>
using namespace std;

template<class Ch, class Tr, class... Args>
auto& operator<<(basic_ostream<Ch, Tr>& os, tuple<Args...> const& t) {
	basic_stringstream<Ch, Tr> ss;
	ss << "(";
	apply([&ss](auto&&... args) {((ss << args << ", "), ...);}, t);
	ss.seekp(-2, ss.cur);
	ss << ")";
	return os << ss.str() ;
}

pair<int,int> fun (int a, int b) { return {a,b}; }

int main() {
	tuple <char, int, float> t('a', 10, 15.5);
	get<0>(t) = 'b';
	cout << get<0>(t) << " " << get<1>(t) << " " << get<2>(t) << endl;
	int elements = tuple_size<decltype(t)>::value; // 3
	
	auto p = fun(1,2);
	cout << p.first << " " << p.second << endl;
	
	// unpack the tuple values into separate variables
	int x, y;
	tie(x, y) = fun(1,2);
	// or using structured binding
	auto [a,b,c] = t; // structured binding
	cout << a << " " << b << " " << c << endl;
	
	tuple <int,char,float> tup1(20,'g',17.5);
	tuple <int,char,float> tup2(30,'f',10.5);
	auto tup3 = tuple_cat(tup1,tup2); // concatenating tuples
	cout << tup3 << endl; // use the template to print the tuple
	// print (20, g, 17.5, 30, f, 10.5)
	return 0;
}
```
