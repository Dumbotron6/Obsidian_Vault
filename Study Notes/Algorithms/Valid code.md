# Arrays

[Find pivot index / Find the middle index](https://leetcode.com/explore/learn/card/array-and-string/201/introduction-to-array/1144/)

The code and leetcode suggested code work but this approach doesn’t look for first match. It first looks in middle, then left, then right.

>[!note]- Code
> ```java
> class Solution {
> 	public int pivotIndex(int[] nums) {
> 		int leftSum = 0;
> 		int rightSum = 0;    
> 		int i = -1;
> 		    int j = nums.length;
> 		    //Find middle
> 		    while(j > i + 2) {
> 		        rightSum += nums[--j];
> 		        if(i < j- 2)
> 		            leftSum += nums[++i];
> 		    }
> 		    int left1;
> 		    int right1;
> 		    int mid = j-1;
> 		    boolean found = (leftSum == rightSum);
> 		
> 		    left1 = leftSum;
> 		    right1 = rightSum;
> 		    //Calculate mid to beginning
> 		    while(!found && i != -1) {
> 		
> 		        leftSum -= nums[i--];
> 		        rightSum += nums[--j];
> 		
> 		        if(leftSum == rightSum) {
> 		            found = true;
> 		            mid = i + 1;
> 		        }
> 		    }
> 		
> 		    i = mid - 1;
> 		    j = mid + 1;
> 		    //Calculate mid to end
> 		    while(!found && j != nums.length) {
> 		        left1 += nums[++i];
> 		        right1 -= nums[j++];
> 		
> 		        if(left1 == right1) {
> 		            found = true;
> 		            mid = j - 1;
> 		        }
> 		    }
> 	
> 		    if(found)
> 		        return mid;
> 		    else
> 		        return -1;
> 	}
> }
> ```

