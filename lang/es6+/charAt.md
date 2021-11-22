## charAt 和 string[]  关于 4字节UTF-8问题

```javascript
var s = '中🎍华革命军'
console.log(s)
console.log(s.charAt(0))
console.log(s.charAt(1) + s.charAt(2))
console.log('s[]', s[0],  s[1]+s[2])
console.log(s.replace('中', '美'))
console.log(s.replace('🎍', '国'))
console.log(s.substring(0, 3))

// emoj 都是 4字节字符，所以chartAt、substring等要特别处理
```



