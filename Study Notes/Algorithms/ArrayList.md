
Efficient solution to below problem. To find if largest number in array is at least twice as large as other elements, find the second largest number and see if its double is less than the largest.
[Largest element twice as others](https://leetcode.com/explore/learn/card/array-and-string/201/introduction-to-array/1147/)

---
For below problem, the second loop is unnecessary. int array automatically fills with 0. The flag is also not necessary as we can just return if the number is less than 9.
[Plus one](https://leetcode.com/explore/learn/card/array-and-string/201/introduction-to-array/1148/)

---
The below problem was solved but took too long and code is also long. Look at discuss section for more efficient solution. It is a medium level problem.
[Diagonal traversal](https://leetcode.com/explore/learn/card/array-and-string/202/introduction-to-2d-array/1167/)

Below problem was solved but took an absurdly long time (3 days). Go through discuss section for efficient solution. Analyze own code for unclear code. It is a medium level problem.
[Sprial matrix](https://leetcode.com/explore/learn/card/array-and-string/202/introduction-to-2d-array/1168/)

>[!note]
>On above two problems, it is important to keep note of the direction of movement using flags.

---
Below problem has more efficient solution in the discuss/solutions submitted by others. Note that we can include if condition inside loops instead of three different loops.
[Add binary](https://leetcode.com/explore/learn/card/array-and-string/203/introduction-to-string/1160/)

>[!tip]
>Key takeaway in the above problem's solution submitted by others is, string values can be subtracted. "1"-"0" gives 1. Here, the ascii values are subtracted.

In below problem, the takeaway is in the first loop. The iterations can be reduced by considering size of both strings.
[Index of substring](https://leetcode.com/explore/learn/card/array-and-string/203/introduction-to-string/1161/)

>[!tip]
>In finding the substring in the above problem, the substring can come after the first discovery of the 0th element - in the middle of the substring being traversed. Ex. "issip" in "missississipi". In this case, even though 4 is crossed, we have to look again from 4 and then 7 will be crossed, check again from 7.

---
For the below problem, you found a solution but time limit exceeded. The optimal solution is way more concise and correct. Helped by discuss section.
[Two sum 2](https://leetcode.com/explore/learn/card/array-and-string/205/array-two-pointer-technique/1153/)

> [!NOTE]- Your solution
> ```java
> class Solution {
>     public int[] twoSum(int[] numbers, int target) {
>         int smp = 0;
>         int loc = 1;
>         int smallest, diff;
>         int[] retArray = new int[2];
>         
>         while(smp < numbers.length) {
>             loc = smp+1;
>             smallest = numbers[smp];
>             diff = target-smallest;
>             while(loc<numbers.length && numbers[loc]<=diff) {
>                 if(numbers[loc]<diff)
>                     loc++;
>                 else if(numbers[loc] == diff) {
>                     retArray[0] = smp+1;
>                     retArray[1] = loc+1;
>                     return retArray;
>                 }
>             }
>             smp++;
>         }
>         return retArray;
>     }
> }
> ```

> [!NOTE]- Optimal solution
> ```java
> class Solution {
>     public int[] twoSum(int[] numbers, int target) {
>         
>         int[] retArray = new int[2];
>         int low = 0;
>         int high = numbers.length-1;
>         while(low<high) {
>             if(numbers[low]+numbers[high]>target)
>                 high--;
>             else if(numbers[low]+numbers[high]<target)
>                 low++;
>             else {
>                 retArray[0] = low+1;
>                 retArray[1] = high+1;
>                 return retArray;
>             }
>         }
>         return retArray;
>     }
> }
> ```

---
For the below problem, you found a solution but it was sub optimal. The approach was correct  but the sum calculation was done again and again which was redundant. After looking at solutions in discuss, the redundant parts were eliminated. It is a medium problem. Took ~2hrs for initial solution.
[Minimum size sub array sum](https://leetcode.com/explore/learn/card/array-and-string/205/array-two-pointer-technique/1299/)

> [!NOTE]- Initial solution
> ```java
> class Solution {
>     public int minSubArrayLen(int target, int[] nums) {
>         
>         int i = 0;
>         int loc = 0;
>         int size = 0;
>         int sum = 0;
>         
>         while(i < nums.length) {
>             if(nums[i]>=target)
>                 return 1;
>             sum+=nums[i++];
>             while(sum>=target) {
>                 if(sum-nums[loc] > target) {
>                     sum-=nums[loc++];
>                     if(size==0 || i-loc<size)
>                         size=i-loc;
>                 } else {
>                     if(size==0 || i-loc<size)
>                         size = i-loc;
>                     i=++loc;
>                     sum=0;
>                 }
>             }
>         }
>         return size;
>     }
> }
> ```

> [!NOTE]- Optimized solution
> ```java
> class Solution {
>     public int minSubArrayLen(int target, int[] nums) {
>         
>         int i = 0;
>         int loc = 0;
>         int size = 0;
>         int sum = 0;
>         
>         while(i < nums.length) {
>             if(nums[i]>=target)
>                 return 1;
>             sum+=nums[i++];
>             while(sum>=target) {
>                 if(size==0 || i-loc<size)
>                     size=i-loc;
>                 sum-=nums[loc++];
>             }
>         }
>         return size;
>     }
> }
> ```

>[!tip]
>The sum was found from sum(i-n). There was no need to calculate again from sum((i+1)-n). We could have simply done sum(i-n) - val(i). This was the redundant part.

---
