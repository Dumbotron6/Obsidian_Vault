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

---
For the below problem, stack approach is correct. Only change would be to not use inbuilt LinkedList. Not exactly an improvement. #Stack
[Valid parentheses](https://leetcode.com/problems/valid-parentheses/description/?source=submission-ac)

>[!tip] Tip
>Check all edge cases before submitting. Two submissions were wasted because edge cases were not tested.

---
In the below problem, using HashSet is valid but takes too long (This was the first solution submitted). In the below solution, the logic is that by using two pointers and the second pointer moves by 2 and the first by 1, in a cyclic linked list, 2 will always catch up to 1. These are also called ==*fast and slow pointers*== ([[../Quick lookup#Fast and slow pointers|^]]). The fast pointer will eventually meet the slow pointer. #HashTable #TwoPointer
[Linked list cycle](https://leetcode.com/problems/linked-list-cycle/description/)

> [!NOTE]- Efficient solution
> ```java
> public class Solution {
>     public boolean hasCycle(ListNode head) {
>         if(head != null) {
>             ListNode header = head;
>             ListNode trailer = head.next;
>             
>             while(header!=null && trailer!=null && trailer.next!=null) {
>                 if(header == trailer)
>                     return true;
> 
>                 header = header.next;
>                 trailer = trailer.next.next;
>             }
>         }
>         return false;
>     }
> }
> ```

---
In the below problem, the logic was correct but the execution was completely inefficient. Recursion can be done more efficiently. #LinkedList #Recursion
[Add two numbers](https://leetcode.com/problems/add-two-numbers/description/)

> [!NOTE]- Your solution
> ```java
> class Solution {
>     public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
>         ListNode retList = new ListNode();
>         ListNode next = retList;
>         add(retList, l1, l2, false, null);
>         return next;
>     }
> 
>     public void add(ListNode retList, ListNode l1, ListNode l2, boolean carry, ListNode prev) {
>         if(l1 != null && l2 != null) {
>             if(carry) {
>                 retList.val += 1;
>                 carry = false;
>             }
>             retList.next = new ListNode();
>             retList.val = retList.val + l1.val + l2.val;
> 
>             if(retList.val > 9) {
>                 retList.val %= 10;
>                 carry = true;
>             }
>             add(retList.next, l1.next, l2.next, carry, retList);
>         } else if(l1 == null && l2 != null) {
>             if(carry) {
>                 l2.val += 1;
>                 carry = false;
>             }
>             retList = l2;
>             prev.next = retList;
>             if(retList.val > 9) {
>                 retList.val %= 10;
>                 carry = true;
>                 if(retList.next == null )
>                     retList.next = new ListNode();
>                 //l2 continue
>                 add(retList.next, l1, l2.next, carry, retList);
>             }
>         } else if (l2 == null && l1 != null) {
>             if(carry) {
>                 l1.val += 1;
>                 carry = false;
>             }
>             retList = l1;
>             prev.next = retList;
>             if(retList.val > 9) {
>                 retList.val %= 10;
>                 carry = true;
>                 if(retList.next == null )
>                     retList.next = new ListNode();
>                 //l1 continue
>                 add(retList.next, l1.next, l2, carry, retList);
>             }
>         } else {
>             if(carry) {
>                 retList.val += 1;
>                 carry = false;
>             }
>             if(retList.val > 9) {
>                 retList.val %= 10;
>                 carry = true;
>                 if(retList.next == null )
>                     retList.next = new ListNode();
>                 //l1 continue
>                 add(retList.next, l1, l2, carry, retList);
>             } else if(retList.val == 0) // no carry. last element is 0
>                 prev.next = null;
>         }
>     }
> }
> ```

> [!NOTE]- Efficient solution
> ```java
> class Solution {
>     public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
>         return helper(l1, l2, 0);
>     }
> 
>     public ListNode helper(ListNode l1, ListNode l2, int carry) {
>             
>         if (l1 == null && l2 == null) {
>             return carry == 1 ? new ListNode(1) : null;
>         }
> 
>         int digitSum;
>         ListNode nextDigit;
> 
>         if (l1 == null) {
>             digitSum = l2.val + carry;
>             nextDigit = helper(null, l2.next, digitSum >= 10 ? 1 : 0);
>         } else if (l2 == null) {
>             digitSum = l1.val + carry;
>             nextDigit = helper(l1.next, null, digitSum >= 10 ? 1 : 0);
>         } else {
>             digitSum = l1.val + l2.val + carry;
>             nextDigit = helper(l1.next, l2.next, digitSum >= 10 ? 1 : 0);
>         }
> 
>         return new ListNode(digitSum % 10, nextDigit);
>     }
> }
> ```

Explanation: helper() returns a new ListNode if both l1 and l2 are 0. If either l1 or l2 is null,  recursion occurs with carry numbers. The return `new ListNode(digitSum % 10, nextDigit);` the end of helper() creates a new node and points assigns the next node, next digit. Not that the helper() called in addTwoNumbers() returns a pointer to the first node. The logic is, even though the last node is the one created first when the recursion ends (`if (l1 == null && l2 == null)`), the previous nodes are created recursively and then the next node is assigned (`new ListNode(digitSum % 10, nextDigit);`).

---
The below problem is a follow up of Linked List Cycle. To returning the starting point of the loop, first determine if loop exists. One correction regarding fast and slow pointers is to always ==start both pointers at the same point==. This was not done in the original problem solution. Once determined that loop exists, move the fast pointer to the beginning of the linked list and then move both pointers at the same speed (by 1). They will meet at the starting point of the loop. #LinkedList 
[Linked list cycle 2](https://leetcode.com/explore/learn/card/linked-list/214/two-pointer-technique/1214/)

> [!NOTE]- Solution
> ```java
>     public ListNode detectCycle(ListNode head) {
>         if(head != null) {
>             ListNode header = head;
>             ListNode trailer = head;
>             while(header != null && trailer != null && trailer.next != null) {
>                 header = header.next;
>                 trailer = trailer.next.next;
>                 if(header == trailer) {
>                     trailer = head;
>                     while(header != trailer) {
>                         header = header.next;
>                         trailer = trailer.next;
>                     }
>                     return header;
>                 }
>             }
>         }
>         return null;
>     }
> ```

The takeaway is that when staring the fast and slow pointers at the same point, they eventually meet at either the 'mid point' or the 'starting point' of the cyclic linked list. This could attributed to the fact that fast pointer moves at twice the speed of the slow pointer. 'Mid point' means distance from the 'first node to the loop node' and 'the meeting node the loop node' is the same. #lookupmore 

---
This problem is a bit tricky. The first(brute) approach is to use a Hashmap to keep track of elements. The second approach is to use a nested loop to check element of the first linked list against the second. For the optimal solution, determine the length of both the linked list. Then move the greater linked list by the difference. This ensures that the length of both linked list is now the same (This length of the two prior to intersection is also same now). Now when we iterate both the linked lists, they will meet at the intersection if any. #LinkedList 
[Intersection of Two Linked Lists](https://leetcode.com/explore/learn/card/linked-list/214/two-pointer-technique/1215/)

---

>[!tip] Linked List tip
>One observation for any linkedlist solution is, if for any reason, the first element has to be removed, (example would be below problem, remove elements), create a dummy node and make dummy.next the head. If the head has to be removed, the dummy.next would then point to head.next. Repeat subsequent elements. So now, when we need to return head after solving the problem, we can return dummy.next.

[Remove Linked List Elements](https://leetcode.com/explore/learn/card/linked-list/219/classic-problems/1207/) #LinkedList 

> [!NOTE]- Solution with dummy node
> ```java
> 	public ListNode removeElements(ListNode head, int val) {
>         ListNode p = new ListNode(-1, head);
>         ListNode ans = p;
>         while (p.next != null) {
>             if (p.next.val == val) {
>                 p.next = p.next.next;
>             } else {
>                 p = p.next;
>             }
>         }
>         return ans.next;
>     }
> ```

This can also be solved via recursion. Look at solution to understand recursion better. #Recursion 

> [!NOTE]- Recursion solution
> ```java
> 	public ListNode removeElements(ListNode head, int val) {
>         if(head==null)
>         {
>             return null;
>         }
>         head.next=removeElements(head.next,val);
>         if (head.val == val)
>         {
>             return head.next;
>         }    
>         else 
>         {
>             return head;
>         }
>     }
> ```