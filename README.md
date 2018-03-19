_data
-------------
* 개인용 파일 입니다.

_each()
-------------
> _each( 값, 반복 조건 ) 
* 조건 함수(iter)에 값(list)을 대입시켜 반복 실행하여 최종값을 반환 합니다.
```
function _each (list,iter) {
	for(var i=0;i < list.length;i++){
		iter(list[i]);
	}
	return list;
}
/*예시*/
console.log(
_each([1,2,3,4],function(val){return val;})
)
/*결과값*/
[1, 2, 3, 4]
```

_filter()
-------------
> _filter( 값, 조건(값.속성 > value) )
* 조건 함수(predi)에 값(list)을 대입시켜 반복실행하여 최종값을 반환 합니다. 내부적으로 _each()함수를 기반으로 실행시킵니다.
```
function _filter (list,predi) {
	var new_list=[];
	_each(list,function (val) {
		if(predi(val)) new_list.push(val);
	});
	return new_list;
}
/*예시*/
var users = [
	{ id: 1, name: 'AA', age: 36 },
	{ id: 2, name: 'BB', age: 35 },
	{ id: 3, name: 'CC', age: 34 },
	{ id: 4, name: 'DD', age: 29 },
	{ id: 5, name: 'EE', age: 28 }
];
console.log(
_filter(users,function(val){return val.age>=30;})
)
/*결과값*/
{id: 1, name: "AA", age: 36}
{id: 2, name: "BB", age: 35}
{id: 3, name: "CC", age: 34}
```

_map()
-------------
> _map( 값, 조건(값.속성+value) )
* 특정 조건(mapper)을 이용하여 값(list)을 변경시켜 반환합니다.
```
function _map (list,mapper) {
	var new_list=[];
	_each(list,function(val){
		new_list.push(mapper(val));
	});
	return new_list;
}
/*예시*/
var users = [
	{ id: 1, name: 'AA', age: 36 },
	{ id: 2, name: 'BB', age: 35 },
	{ id: 3, name: 'CC', age: 34 },
	{ id: 4, name: 'DD', age: 29 },
	{ id: 5, name: 'EE', age: 28 }
];
console.log(
_map(users,function(val){return val.age+10;})
)
/*결과값*/
[46, 45, 44, 39, 38]
/*응용*/
console.log(
_map(
	_filter(users,function(val){return val.age >= 30;}),
	function(val){return val.age+10;}
)
)
/*결과값*/
[46, 45, 44]
```

_get()
-------------
> _get( 값, 속성명 )
* 인자(obj,key)를 이용하여 값과 속성을 반환합니다.
```
function _get (obj,key) {
	return obj==null?undefined:obj[key];
}
/*예시*/
var users = [
	{ id: 1, name: 'AA', age: 36 },
	{ id: 2, name: 'BB', age: 35 },
	{ id: 3, name: 'CC', age: 34 },
	{ id: 4, name: 'DD', age: 29 },
	{ id: 5, name: 'EE', age: 28 }
];
var user1=users[0];
console.log(_get(user1,'name'));//==user1.name
/*결과값*/
["AA"]
```

_curry()
-------------
> _curry( 함수, 함수 )
* 2개의 인자(a,b)를 이용하여 원하는 시점에 각 함수를 실행 시킵니다.
```
function _curry (fn) {
	return function (a,b) {
		return (arguments.length==2)?fn(a,b):function(b){return fn(a,b);};
	}
}
/*예시*/
var add=_curry(function(a,b){return a+b;});
var add10=(add(10));
console.log(add10));
console.log(add10(20));
console.log(add(10)(20));
/*결과값*/
add10 // ƒ (b){return fn(a,b);}
add10(20) // 30
add(10)(20) // 30
```

_curryr()
-------------
> _curryr( 함수 )
* _curry()함수를 반대로 사용하여 최종 실행값을 두번째 인자함수로 값을 반환 시킵니다.
(a, b)의 순서를 가지는 curry함수의 최종 리턴값을 (b, a)로 변경하여 함수내 결과함수를 서로 바꾸게 됩니다.
```
function _curryr (fn) {
	return function(a,b){
		return (arguments.length==2)?fn(a,b):function(b){return fn(b,a);};
	}
}
/*예시*/
var users = [
	{ id: 1, name: 'AA', age: 36 },
	{ id: 2, name: 'BB', age: 35 },
	{ id: 3, name: 'CC', age: 34 },
	{ id: 4, name: 'DD', age: 29 },
	{ id: 5, name: 'EE', age: 28 }
];

var user1=users[0];
console.log(_get(user1,'name'));// == user1.name // AA

var _get=_curryr(_get);//_curryr 적용
var get_name=_get('name');
console.log(get_name(user1));//AA
console.log(_get('name')(user1));//AA
/*결과값*/
_get(user1,'name') // AA
get_name(user1) // AA
_get('name')(user1) // AA
/*응용*/
console.log(
_map(
	_filter(users,function(val){return val.age >= 30}),
	_get('name')//==function(val){return val.name}
)
)
/*결과값*/
["AA", "BB", "CC"]
```

_reduce()
-------------
> _reduce( 값, 조건, 최초값 )
* 최초값을 설정하거나 또는 0번 인덱스의 값으로 설정 됩니다. 그 후 조건함수(iter)를 대입하여 조회한후 결과값으로 다시 조회(iter)를 하여 값을 반환합니다.
```
function _reduce (list,iter,memo) {
	if(arguments.length==2){
		memo=list[0];
		list=_rest(list);
	}
	_each(list,function(val){
		memo=iter(memo,val);
	});
	return memo;
}
/*예시*/
console.log(
_reduce([1,2,3,4],function(a,b){return a+b;})
)
/*결과값*/
10
```

_rest()
-------------
> _rest( 값(배열), 슬라이드 index )
* 함수 내 slice 메서드를 사용하여 결과값을 반환합니다.
```
function _rest(list, num) {
	return slice.call(list,num || 1);
}
/*예시*/
console.log(
_rest([1,2,3,4],2)
)
/*결과값*/
[3,4]
```

_pipe()
-------------
> _pipe( 함수 )( 값 )
* 값을 보내어 순차적으로 결과값을 넘기면서 함수를 실행합니다.
```
function _pipe () {
	var fns=arguments;
	return function (arg) {
		return _reduce(fns,function (arg,fn) {
			return fn(arg)
		},arg);
	}
};
/*예시*/
console.log(
	_pipe(
		function(a){return a+1;},
		function(a){return a*2;},
		function(a){return a*a;}
	)(1)
)
/*결과값*/
16
```

_go()
-------------
> _go( 값, 함수... )
* 값을 받아 _pipe()로 결과값을 전달하여 실행합니다.
```
function _go (arg) {
	var fns=_rest(arguments);
	return _pipe.apply(null,fns)(arg);//.apply메소드를 사용, 배열값 보냄
}
/*예시*/
_go(2,
	function(a) {return a+1;},
	function(a) {return a*2;},
	function(a) {return a*a;},
	console.log
)
/*결과값*/
36
/*응용*/
var users = [
	{ id: 1, name: 'AA', age: 36 },
	{ id: 2, name: 'BB', age: 35 },
	{ id: 3, name: 'CC', age: 34 },
	{ id: 4, name: 'DD', age: 29 },
	{ id: 5, name: 'EE', age: 28 }
];
_go(
	users,
	_filter(function(val){return val.age>=30}),
	_map(_get('name')),//==function(val){return val.name;}
	console.log
)
/*결과값*/
["AA","BB","CC"]
```

_is_object()
-------------
> _is_object( 값 )
* 값(obj)을 조회하여 참 or 거짓으로 결과값을 반환
```
function _is_object (obj) {
	return typeof obj=='object' && !!obj; // true and flase로 결과값 출력
}
```

_keys()
-------------
> _keys( 값 )
* object 및 array의 key 값을 array로 반환합니다.
```
function _keys (obj) {
	return _is_object(obj) ? Object.keys(obj) : [];
}
/*예시*/
console.log(_keys({"10":1,"20":2,"30":3}))
/*결과값*/
["10", "20", "30"]
```

_values()
-------------
> _values( 값 )
* 값(data)을 전달하여 값(data)내 모든(value) 정보를 반환합니다.
```
function _values (data) {
	return _map(data,_identity)
}
/*예시*/
var users = [
	{ id: 1, name: 'AA', age: 36 },
	{ id: 2, name: 'BB', age: 35 },
	{ id: 3, name: 'CC', age: 34 },
	{ id: 4, name: 'DD', age: 29 },
	{ id: 5, name: 'EE', age: 28 }
];
console.log(_values(users))
/*결과값*/
{ id: 1, name: 'AA', age: 36 }
{ id: 2, name: 'BB', age: 35 }
{ id: 3, name: 'CC', age: 34 }
{ id: 4, name: 'DD', age: 29 }
{ id: 5, name: 'EE', age: 28 }
```

_identity()
-------------
> _identity( 값 )
* 값(val)을 전달하여 값(val)을 그대로 반환합니다.
```
function _identity (val) {
	return val;
}
/*예시*/
console.log(_identity(users))
/*결과값*/
{ id: 1, name: 'AA', age: 36 }
{ id: 2, name: 'BB', age: 35 }
{ id: 3, name: 'CC', age: 34 }
{ id: 4, name: 'DD', age: 29 }
{ id: 5, name: 'EE', age: 28 }
```

_negate()
-------------
> _negate( 함수 )
* 함수의 반대 결과를 반환합니다.
```
function _negate (func) {
	return function(val){
		return !func(val);
	}
}
```

_reject()
-------------
> _reject( 값, 조건 )
* 값(data)에 조건(predi)을 대입하여 조건에 일치하는 값을 제외 후 반환합니다.
```
function _reject (data,predi) {
	return _filter(data,_negate(predi));
}
/*예시*/
console.log(
_reject(
	users,
	function(val){return val.age>30}
)
)
/*결과값*/
{ id: 4, name: 'DD', age: 29 }
{ id: 5, name: 'EE', age: 28 }
```

_find()
-------------
> _find( 값, 조건 )
* 값(list)을 조건(predi)에 대입하여 최초 일치하는 결과값을 반환합니다.
```
function _find (list,predi) {
	var keys=_keys(list);
	for(var i=0,len=keys.length;i < len;i++){
		var val=list[keys[i]];
		if(predi(val)==true) return val;
	}
}
/*예시*/
console.log(
_find(
	users,
	function(val){return val.age < 30}
)
)
/*결과값*/
{id: 4, name: "DD", age: 29}
/*응용*/
console.log(
_get(
	_find(
		users,
		function(val){return val.id == 2}
	),
	'name'
)
)
/*결과값*/
BB
```

_find_index()
-------------
> _find_index( 값, 조건 )
* 값(list)을 조건(predi)에 대입하여 일치하는 값의 index를 반환합니다.
```
function _find_index (list,predi) {
	var keys=_keys(list);
	for(var i=0,len=keys.length;i < len;i++){
		if(predi(list[keys[i]])) return i;
	}
	return -1;
}
/*예시*/
console.log(
_find_index(
	users,
	function(user){return user.id==4;}
)
)
/*결과값*/
3
/*응용*/
_go(
	users,
	_find_index(
		function(val){return val.id==3;}
	),
	console.log
)
/*결과값*/
2
```

_some()
-------------
> _some( 값, 조건 )
* 모든값(data)중 조건(predi)에 한번이라도 일치하면 true 일치하지 않으면 false를 반환합니다.
```
function _some (data,predi) {
	return _find_index(data,predi||_identity) != -1;
}
/*예시*/
_some([1, 2, 0, 10])//true
_some([null, false, 0])//false
console.log(
_some(
	[1,2,5,10,20],
	function(val){return val > 10;}
)
)
/*결과값*/
true
```

_every()
-------------
> _every( 값, 조건 )
* 모든값(data)이 조건(predi)에 일치하면 true 일치하지 않으면 false를 반환합니다.
```
function _every (data,predi) {
	return _find_index(data, _negate(predi || _identity)) == -1;
}
/*예시*/
console.log(
_every(
	[1,2,5,10,20],
	function(val){return val > 10;}
)
)
/*결과값*/
false
```

_min()
-------------
> _min( 값 )
* array 값(data)중 최소값을 반환합니다.
```
function _min (data) {
	return _reduce(data,function(a,b){
		return a < b?a:b;
	})
}
/*예시*/
console.log(
_min([1,2,4,10,5,-4])
)
/*결과값*/
-4
```

_max()
-------------
> _max( 값 )
* array 값(data)중 최대값을 반환
```
function _max (data) {
	return _reduce(data,function(a,b){
		return a > b?a:b;
	})
}
/*예시*/
console.log(
_max([1,2,4,10,5,-4])
)
/*결과값*/
10
```

_min_by()
-------------
> _min_by( 값, 함수 )
* 모든값(data)을 메소드(iter)에대입하여 결과값의 최소값을 반환합니다.
```
function _min_by (data,iter) {
	return _reduce(data,function(a,b){
		return iter(a) < iter(b)?a:b;
	})
}
/*예시*/
console.log(
_min_by([1,2,4,10,5,-4],Math.abs) // Math.abs : 절대값
)
/*결과값*/
1
```

_max_by()
-------------
> _max_by( 값, 함수 )
* 모든값(data)을 메소드(iter)에대입하여 결과값의 최대값을 반환합니다.
```
function _max_by (data,iter) {
	return _reduce(data,function(a,b){
		return iter(a)>iter(b)?a:b;
	})
}
/*예시*/
console.log(
_max_by([1,2,4,10,5,-4],Math.abs)
)
/*결과값*/
10
```

_group_by()
-------------
> _group_by( 함수 )
* 조건(iter)에 일치하는 값을 기준(row)으로 그룹을 지정하여 반환합니다.
```
function _group_by(data,iter){
	return _reduce(data,function(grouped,val){
		return _push(grouped,iter(val),val);
	},{});
};
/*예시*/
var users = [
	{ id: 1, name: 'AA', age: 36 },
	{ id: 2, name: 'BB', age: 35 },
	{ id: 3, name: 'CC', age: 34 },
	{ id: 4, name: 'DD', age: 29 },
	{ id: 5, name: 'EE', age: 28 }
];
_go(
	users,
	_group_by(_get('age')),
	console.log
)
/*결과값*/
28,{id: 5, name: "EE", age: 28}
29,{id: 4, name: "DD", age: 29}
34,{id: 3, name: "CC", age: 34}
35,{id: 2, name: "BB", age: 35}
36,{id: 1, name: "AA", age: 36}
```

_count_by()
-------------
> _count_by( 값, 함수 )
* 조건(iter)에 일치하는 값(data)을 묶어 총 크기를 반환합니다.
```
function _count_by(data,iter){
	return _reduce(data,function(count,val){
		return _inc(count,iter(val));
	},{});
}
/*예시*/
var users = [
	{ id: 1, name: 'AA', age: 36 },
	{ id: 2, name: 'BB', age: 35 },
	{ id: 3, name: 'CC', age: 34 },
	{ id: 4, name: 'DD', age: 29 },
	{ id: 5, name: 'EE', age: 28 }
];
console.log(
_count_by(
	users,function(val){return val.age;}
)
)
/*결과값*/
{28: 1, 29: 1, 34: 1, 35: 1, 36: 1}
```

