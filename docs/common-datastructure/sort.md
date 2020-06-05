# 快速排序

快排的核心思路/方法是**分区**，分区函数决定最终效果的好坏。下面介绍集中分区方法，自己只需**深入理解记忆其中一个，降低负担**。

## 默认左端为枢纽
```cpp
int partition(vector<int>& nums, int low, int high) {
    int i, pivot=nums[left], pos=left;
    swap(nums[high], nums[low]);

    for(i=low; i<=high; i++)
        if(nums[i] < pivot)
            swap(nums[i], nums[pos++]);
        
    swap(nums[pos], nums[high]);
    return pos;
}
```

两端逼近，参考[最常用的排序——快速排序](https://wiki.jikexueyuan.com/project/easy-learn-algorithm/fast-sort.html)中的图解
```cpp
int partition(vector<int>& nums, int low, int right) {
    int i=low, j=high+1, pivot=nums[low];
    while(i < j) {
        while(i<high && nums[++i] <= pivot) {}
        while(j>low && nums[--j] >= pivot) {}

        if(i < j) swap(nums[i], nums[j]);
    }

    swap(nums[j], nums[low]);
    return j;
}
```

## 完整代码
```cpp
#include <vector>
#include <stdio.h>
#include <stdlib.h>

using namespace std;

int partition(vector<int>& nums, int low, int high) {
    int i=low, j=high+1;
    int pivot = nums[low];

    while(i<j) {
        while(i<high && nums[++i] <= pivot) {}
            // if(i == high) break;
            
        while(j>low && nums[--j] >= pivot) {}
            // if(j == low) break;
        
        if(i<j) swap(nums[i], nums[j]);
    }

    swap(nums[j], nums[low]);
    return j;
}

int partition1(vector<int>& nums, int low, int high) {
    int pivot = nums[low];
    swap(nums[high], nums[low]);
    int pos = low;
    for(int i=low; i<=high; i++) {
        if(nums[i] < pivot)
            swap(nums[i], nums[pos++]);
    }

    swap(nums[pos], nums[high]);
    return pos;
}

void shullfle(vector<int>& nums) {
    int i, j;
    for(i=nums.size()-1; i>=0; i--) {
        j = rand() % (i+1);
        swap(nums[i], nums[j]);
    }
}

int select(vector<int>& nums, int k) {
    if(k<1 || k>nums.size()) return -1;
    k--;
    // shullfle(nums);
    int pos, low=0, high=nums.size()-1;
    while(low < high) {
        pos = partition(nums, low, high);
        if(k == pos) return nums[pos];
        else if(k < pos) high = pos-1;
        else low = pos+1;
    }

    return nums[low];
}

void quick(vector<int>& nums, int low, int high) {
    if(low >= high) return;
    int pos = partition1(nums, low, high);
    quick(nums, low, pos-1);
    quick(nums, pos+1, high);
}

void show(vector<int>& nums) {
    int i, size=nums.size();
    for(i=0; i<size; i++) {
        printf("%d", nums[i]);
        if(i==size-1) printf("\n");
        else printf(" ");
    }
}

int main() {
    vector<int> nums = {5,8,1,4,2,9,70,4};
    quick(nums, 0, nums.size()-1);
    show(nums);

    shullfle(nums);
    show(nums);
    printf("%d\n", select(nums, 1));
    printf("%d\n", select(nums, 2));
    printf("%d\n", select(nums, 3));
    printf("%d\n", select(nums, 4));

    return 0;
}
```

# 归并排序
归并排序的采用**分-合**的思想，先把数组从中间分成两部分，然后合并到一起的时候进行排序。

代码如下：
```cpp
class Merge {
private:
    vector<int> aux;
public:
    void merge(vector<int>& nums, int low, int mid, int high) {
        int i, j, k;
        for(i=low; i<=high; i++) aux[i] = nums[i];
        
        i=low, j=mid+1;
        for(k=low; k<=high; k++) {
            if(i>mid) nums[k] = aux[j++];
            else if(j>high) nums[k] = aux[i++];
            else if(aux[i] < aux[j]) nums[k] = aux[i++];
            else nums[k] = aux[j++];
        }
    }

    void helper(vector<int>& nums, int low, int high) {
        if(low >= high) return;

        int mid = (low+high)/2;
        helper(nums, low, mid);
        helper(nums, mid+1, high);
        merge(nums, low, mid, high);
    }

    void sort(vector<int>& nums) {
        int size = nums.size();
        aux = vector<int>(size, 0);
        helper(nums, 0, size-1);
    }

    void shullfle(vector<int>& nums) {
        int i, j;
        for(j=nums.size()-1; j>=0; j--) {
            i = rand()%(j+1);
            swap(nums[i], nums[j]);
        }
    }

    void show(vector<int>& nums) {
        int i, size=nums.size();
        for(i=0; i<size; i++) {
            printf("%d", nums[i]);
            if(i != size-1) printf("-");
            else printf("\n");
        }
    }
};

int main() {
    vector<int> nums = {5,8,1,4,2,9,70,4};

    Quick quick;
    quick.show(nums);
    quick.sort(nums, 0, nums.size()-1);
    quick.show(nums);
    quick.shullfle(nums);
    quick.show(nums);

    Merge merge;
    merge.show(nums);
    merge.sort(nums);
    merge.show(nums);

    printf("select:%d\n", quick.select(nums, 2));
    return 0;
}
```