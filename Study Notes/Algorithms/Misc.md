In the below problem, the solution could have been more efficient. In Roman numbers, if at anytime a lower number comes before a higher number, subtract it. No need to check for combinations(This was done in the submitted solution). #HashTable
[Roman to Integer](https://leetcode.com/problems/roman-to-integer/description/)

>[!note]- Efficient solution
> ```java
>     int res = 0;
> 	int prev = 0;
> 
> 	for(int i= s.length()-1;i>=0;i--){
> 
> 		int current = map.get(s.charAt(i));
> 		if(current<prev){
> 			res-=current;
> 		}else{
> 			res+=current;
> 		}
> 		prev = current;
> 	}
> ```

For the below problem, stack approach is correct. Only change would be to not use inbuilt LinkedList. Not exactly an improvement. #Stack
[Valid parentheses](https://leetcode.com/problems/valid-parentheses/description/?source=submission-ac)

>[!tip] Tip
>Check all edge cases before submitting. Two submissions were wasted because edge cases were not tested.

