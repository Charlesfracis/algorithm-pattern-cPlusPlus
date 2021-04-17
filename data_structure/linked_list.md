# 链表

## 基本技能

链表相关的核心点

- null/nil 异常处理
- dummy node 哑巴节点
- 快慢指针
- 插入一个节点到排序链表
- 从一个链表中移除一个节点
- 翻转链表
- 合并两个链表
- 找到链表的中间节点

## 常见题型

### [remove-duplicates-from-sorted-list](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

> 给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。

```c++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (!head) return head;

        ListNode* curr = head;
        while (curr->next) // 只需遍历到倒数第二个节点
        {
            if (curr->val == curr->next->val) // 有重复元素
            {
                curr->next = curr->next->next;
            }
            else curr = curr->next;
        }

        return head;
    }
};
```

### [remove-duplicates-from-sorted-list-ii](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

> 给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中   没有重复出现的数字。

思路：链表头结点可能被删除，所以用 dummy node 辅助删除

```c++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (!head) return head;
        // 由于头结点可能被删除因此需要一个指向head的哑结点
        ListNode* dummy = new ListNode(0, head);
        ListNode* curr = dummy;
        
        while (curr->next && curr->next->next)
        {
            if (curr->next->val == curr->next->next->val) // 发现重复值
            {
                int x = curr->next->val; // 记录重复的数字
                // 删除重复的节点
                while (curr->next && curr->next->val == x)
                {
                    curr->next = curr->next->next;
                }
            }
            else curr = curr->next;
        }

        return dummy->next; // 哑结点的next是头节点
    }
};
```

注意点
• A->B->C 删除 B，A.next = C
• 删除用一个 Dummy Node 节点辅助（允许头节点可变）
• 访问 X.next 、X.value 一定要保证 X != nil

### [reverse-linked-list](https://leetcode-cn.com/problems/reverse-linked-list/)

> 反转一个单链表。

思路：用一个 prev 节点保存向前指针，temp 保存向后的临时指针

```c++
class Solution {
public:
    // 用一个 prev 节点保存向前指针，temp 保存向后的临时指针
    ListNode* reverseList(ListNode* head) {

        ListNode* pre = nullptr;
        ListNode* cur = head;

        while (cur)
        {
            // 保存当前head.Next节点，防止重新赋值后被覆盖
            // 一轮之后状态：nullptr<-1 2->3->4
            //               prev   head
            ListNode* tmp = cur->next; //下个待考察的点
            cur->next = pre; // 当前点反转指向pre
            pre = cur; // pre移动到当前点
            cur = tmp; // 移动到下个点
        }

        return pre;
    }
};
```

### [reverse-linked-list-ii](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

> 反转从位置  *m*  到  *n*  的链表。请使用一趟扫描完成反转。

思路：先遍历到 m 处，翻转，再拼接后续，注意指针处理

```c++
class Solution {
public:
    void reverseLinkedList(ListNode* head)
    {
        ListNode* prev = nullptr;
        ListNode* curr = head;
        while (curr)
        {
            ListNode* temp = curr->next;
            curr->next = prev;
            prev = curr;
            curr = temp;
        }
    }

    ListNode* reverseBetween(ListNode* head, int left, int right) {
        // 头结点可能变化, 使用哑结点
        ListNode* dummy = new ListNode(0, head);

        ListNode* pre = dummy;
        // 第 1 步：从虚拟头节点走 left - 1 步，来到 left 节点的前一个节点
        for (int i = 0; i < left - 1; i ++) {
            pre = pre->next;
        }
        // 第 2 步：从 pre 再走 right - left + 1 步，来到 right 节点
        ListNode* rightNode = pre;
        for (int i = 0; i < right - left + 1; i ++) {
            rightNode = rightNode->next;
        }
        // 第 3 步：切断出一个子链表（截取链表）
        ListNode* leftNode = pre->next;
        ListNode* curr = rightNode->next;
        // 注意：切断链接
        pre->next = nullptr;
        rightNode->next = nullptr;
        // 第 4 步：反转链表的子区间
        reverseLinkedList(leftNode);
        // 第 5 步：接回到原来的链表中
        pre->next = rightNode; // 左边接到原来的右边
        leftNode->next = curr; // 原来的左边现在的右边后面接上现在后面的
        return dummy->next;
    }
};
```

### [merge-two-sorted-lists](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

> 将两个升序链表合并为一个新的升序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

思路：通过 dummy node 链表，连接各个元素

```c++
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode* preHead = new ListNode(0); // 哨兵节点
        ListNode* prev = preHead;

        while (l1 && l2)
        {
            if (l1->val < l2->val)
            {
                prev->next = l1;
                l1 = l1->next;
            }
            else
            {
                prev->next = l2;
                l2 = l2->next;
            }
            prev = prev->next;
        }

        // 合并后 l1 和 l2 最多只有一个还未被合并完，直接将链表末尾指向未合并完的链表即可
        prev->next = l1 == nullptr ? l2 : l1;

        return preHead->next;
    }
};
```

### [partition-list](https://leetcode-cn.com/problems/partition-list/)

> 给定一个链表和一个特定值 x，对链表进行分隔，使得所有小于  *x*  的节点都在大于或等于  *x*  的节点之前。

思路：将大于 x 的节点，放到另外一个链表，最后连接这两个链表

```c++
class Solution {
public:
    ListNode* partition(ListNode* head, int x) {
        ListNode* small = new ListNode(0);
        ListNode* smallHead = small;
        ListNode* large = new ListNode(0);
        ListNode* largeHead = large;

        while (head)
        {
            if (head->val < x)
            {
                small->next = head;
                small = small->next;
            }
            else
            {
                large->next = head;
                large = large->next;
            }
            head = head->next;
        }

        large->next = nullptr; // 截断large最后一个节点
        small->next = largeHead->next; // 两端链表相接
        return smallHead->next;
    }
};
```

哑巴节点使用场景

> 当头节点不确定的时候，使用哑巴节点

### [sort-list](https://leetcode-cn.com/problems/sort-list/)

> 在  *O*(*n* log *n*) 时间复杂度和常数级空间复杂度下，对链表进行排序。

思路：归并排序，找中点和合并操作

```c++
class Solution {
public:
    // 快慢指针找到中点
    ListNode* findMid(ListNode* head)
    {
        ListNode* slow = head;
        ListNode* fast = head;
        ListNode* pre = nullptr;

        while (fast && fast->next)
        {
            pre = slow;
            slow = slow->next;
            fast = fast->next->next;
        }
        // split 断开前后两边
        pre->next = nullptr;
        return slow;
    }
    // 合并两个有序链表
    ListNode* mergeTwoList(ListNode* l1, ListNode* l2)
    {
        if (!l1) return l2;
        if (!l2) return l1;

        if (l1->val < l2->val)
        {
            l1->next = mergeTwoList(l1->next, l2);
            return l1;
        }
        else
        {
            l2->next = mergeTwoList(l1, l2->next);
            return l2;
        }
    }
    // 归并排序, 分解为两个单链表有序合并
    ListNode* mergeSort(ListNode* head)
    {
        if (!head->next) return head;

        ListNode* mid = findMid(head);
        ListNode* l1 = mergeSort(head);
        ListNode* l2 = mergeSort(mid);
        return mergeTwoList(l1, l2);
    }

    ListNode* sortList(ListNode* head) {
        if (!head) return nullptr;
        return mergeSort(head);
    }
};
```

注意点

- 快慢指针 判断 fast 及 fast.Next 是否为 nil 值
- 递归 mergeSort 需要断开中间节点
- 递归返回条件为 head 为 nil 或者 head.Next 为 nil

### [reorder-list](https://leetcode-cn.com/problems/reorder-list/)

> 给定一个单链表  *L*：*L*→*L*→…→*L\_\_n*→*L*
> 将其重新排列后变为： *L*→*L\_\_n*→*L*→*L\_\_n*→*L*→*L\_\_n*→…

思路：找到中点断开，翻转后面部分，然后合并前后两个链表

```c++
class Solution {
public:
    // 找到中点，右半段反转，交叉合并
    void reorderList(ListNode* head) {
        if (!head) return;

        ListNode* mid = findMid(head);
        ListNode* l1 = head;
        ListNode* l2 = mid->next;
        mid->next = nullptr; // 截断
        l2 = reverseList(l2);
        // 交叉合并
        ListNode* l1Temp;
        ListNode* l2Temp;
        while (l1 && l2)
        {
            l1Temp = l1->next;
            l2Temp = l2->next;

            l1->next = l2;
            l1 = l1Temp;

            l2->next = l1;
            l2 = l2Temp;
        }
        // l1 即是head
    }

    ListNode* findMid(ListNode* head)
    {
        ListNode* slow = head;
        ListNode* fast = head;
        ListNode* pre = nullptr;

        while (fast && fast->next)
        {
            pre = slow;
            slow = slow->next;
            fast = fast->next->next;
        }
        return slow;
    }

    ListNode* reverseList(ListNode* head)
    {
        ListNode* prev = nullptr;
        ListNode* curr = head;
        while (curr)
        {
            ListNode* temp = curr->next;
            curr->next = prev;
            prev = curr;
            curr = temp;
        }
        return prev;
    }
};
```

### [linked-list-cycle](https://leetcode-cn.com/problems/linked-list-cycle/)

> 给定一个链表，判断链表中是否有环。

思路：快慢指针，快慢指针相同则有环，证明：如果有环每走一步快慢指针距离会减 1
![fast_slow_linked_list](https://img.fuiboom.com/img/fast_slow_linked_list.png)

```c++
class Solution {
public:
    // 快慢指针，快慢指针相同则有环，证明：如果有环每走一步快慢指针距离会减 1
    bool hasCycle(ListNode *head) {
        if (!head || !head->next) return false;

        ListNode* slow = head;
        ListNode* fast = head->next;

        while (slow != fast)
        {
            if (!fast || !fast->next) return false;
            slow = slow->next;
            fast = fast->next->next;
        }
        
        return true;
    }
};
```

### [linked-list-cycle-ii](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

> 给定一个链表，返回链表开始入环的第一个节点。  如果链表无环，则返回  `null`。

思路：用一个set来存储当前遍历过的链表，有环的话则会碰到相同的节点，即入环的第一个点
![cycled_linked_list](https://img.fuiboom.com/img/cycled_linked_list.png)

```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        unordered_set<ListNode*> visited;
        // 有环的话会一直运行，碰到相同的就是入环的第一个点
        while (head)
        {
            if (visited.count(head)) return head;
            visited.insert(head);
            head = head->next;
        }
        
        return nullptr;
    }
};
```

### [palindrome-linked-list](https://leetcode-cn.com/problems/palindrome-linked-list/)

> 请判断一个链表是否为回文链表。

```c++
class Solution {
public:

    ListNode* findMid(ListNode* head)
    {
        ListNode* slow = head;
        ListNode* fast = head;

        while (fast->next && fast->next->next) //坑 都是fast
        {
            slow = slow->next;
            fast = fast->next->next;
        }

        return slow;
    }

    ListNode* reverseList(ListNode* head)
    {
        ListNode* pre = nullptr;
        ListNode* curr = head;

        while (curr)
        {
            ListNode* temp = curr->next;
            curr->next = pre;
            pre = curr;
            curr = temp;
        }

        return pre;
    }

    // 快慢指针找到链表中点，反转后半段链表，看是否相同
    bool isPalindrome(ListNode* head) {
        if (!head || !head->next) return true;

        ListNode* mid = findMid(head);
        ListNode* reverse_mid = reverseList(mid->next); //坑 next

        while(reverse_mid)
        {
            if (reverse_mid->val != head->val) return false;
            else{
                reverse_mid = reverse_mid->next;
                head = head->next;
            }
        }

        return true;
    }
};
```

### [copy-list-with-random-pointer](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)

> 给定一个链表，每个节点包含一个额外增加的随机指针，该指针可以指向链表中的任何节点或空节点。
> 要求返回这个链表的 深拷贝。

思路：1、hash 表存储指针，2、dfs

```c++
class Solution {
public:
    // 利用哈希表储存已经遍历的节点，防止死循环
    unordered_map<Node*, Node*> visited;
    Node* copyRandomList(Node* head) {
        if(!head) return head;

        if(visited.find(head) != visited.end())
            return visited[head];
        Node* cloneNode = new Node(head->val);
        visited[head] = cloneNode;

        cloneNode->next = copyRandomList(head->next);
        cloneNode->random = copyRandomList(head->random);
        return cloneNode;
    }
};
```

## 总结

链表必须要掌握的一些点，通过下面练习题，基本大部分的链表类的题目都是手到擒来~

- nullptr 异常处理
- dummy node 哑巴节点
- 快慢指针
- 插入一个节点到排序链表
- 从一个链表中移除一个节点
- 翻转链表
- 合并两个链表
- 找到链表的中间节点

## 练习

- [ ] [remove-duplicates-from-sorted-list](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)
- [ ] [remove-duplicates-from-sorted-list-ii](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)
- [ ] [reverse-linked-list](https://leetcode-cn.com/problems/reverse-linked-list/)
- [ ] [reverse-linked-list-ii](https://leetcode-cn.com/problems/reverse-linked-list-ii/)
- [ ] [merge-two-sorted-lists](https://leetcode-cn.com/problems/merge-two-sorted-lists/)
- [ ] [partition-list](https://leetcode-cn.com/problems/partition-list/)
- [ ] [sort-list](https://leetcode-cn.com/problems/sort-list/)
- [ ] [reorder-list](https://leetcode-cn.com/problems/reorder-list/)
- [ ] [linked-list-cycle](https://leetcode-cn.com/problems/linked-list-cycle/)
- [ ] [linked-list-cycle-ii](https://leetcode-cn.com/problems/linked-list-cycle-ii/)
- [ ] [palindrome-linked-list](https://leetcode-cn.com/problems/palindrome-linked-list/)
- [ ] [copy-list-with-random-pointer](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)
