Efficient solution to below problem. To find if largest number in array is at least twice as large as other elements, find the second largest number and see if its double is less than the largest.
[Largest element twice as others](https://leetcode.com/explore/learn/card/array-and-string/201/introduction-to-array/1147/)

For below problem, the second loop is unnecessary. int array automatically fills with 0. The flag is also not necessary as we can just return if the number is less than 9.
[Plus one](https://leetcode.com/explore/learn/card/array-and-string/201/introduction-to-array/1148/)

The below problem was solved but took too long and code is also long. Look at discuss section for more efficient solution. It is am medium level problem.
[Diagonal traversal](https://leetcode.com/explore/learn/card/array-and-string/202/introduction-to-2d-array/1167/)

Below problem was solved but took an absurdly long time (3 days). Go through discuss section for efficient solution. Analyze own code for unclear code. It is a medium level problem.
[Sprial matrix](https://leetcode.com/explore/learn/card/array-and-string/202/introduction-to-2d-array/1168/)

>[!note]
>On above two problems, it is important to keep note of the direction of movement using flags.

Below problem has more efficient solution in the discuss/solutions submitted by others. Note that we can include if condition inside loops instead of three different loops.
[Add binary](https://leetcode.com/explore/learn/card/array-and-string/203/introduction-to-string/1160/)

>[!tip]
>Key takeaway in the above problem's solution submitted by others is, string values can be subtracted. "1"-"0" gives 1. Here, the ascii values are subtracted.

In below problem, the takeaway is in the first loop. The iterations can be reduced by considering size of both strings.
[Index of substring](https://leetcode.com/explore/learn/card/array-and-string/203/introduction-to-string/1161/)

>[!tip]
>In finding the substring in the above problem, the substring can come after the first discovery of the 0th element - in the middle of the substring being traversed. Ex. "issip" in "missississipi". In this case, even though 4 is crossed, we have to look again from 4 and then 7 will be crossed, check again from 7.

