# 中点

- mid = (L + R) / 2;//可能会溢出
- mid = L + ((R - L)) / 2;//不会溢出

- mid = L + ((R - L) >> 1);//更快

#  归并排序

- 分析：

  运用递归将数组进行左右拆分，然后分别进行有序，然后进行合并

- 事件复杂度O(NlogN)

- 例子

  5 8 1 2 7 4 6 3  //原数据
  5 8 1 2 7 4 6 3 
  5 8 1 2 7 4 6 3 
  **1 2 5 8** 7 4 6 3 //合并
  1 2 5 8 **4 7** 6 3 //左有序
  1 2 5 8 4 7 **3 6** //右有序
  1 2 5 8 **3 4 6 7** //合并
  **1 2 3 4 5 6 7 8** //合并
  1 2 3 4 5 6 7 8
  
  
  
- ```c++
  //归并排序
  void mergeSort(int arr[], int L, int R)
  {
      if (L == R)
          return;
      int mid = L + ((R - L) >> 1);
      mergeSort(arr, L, mid);//左有序
      mergeSort(arr, mid + 1, R);//右有序
      merge(arr, L, mid, R);//合并
  }
  
  //归并
  void merge(int arr[], int L, int mid, int R)
  {
      int help[R - L + 1];//辅助数组
      int cnt = 0;//指向辅助数组的位置
      int p1 = L;//指向左边
      int p2 = mid + 1;//指向右边的位置
  
      while(p1 <= mid && p2 <= R)//谁小谁在左边
          help[cnt++] = arr[p1] <= arr[p2] ? arr[p1++] : arr[p2++];
  
      while(p1 <= mid)//装入剩下的部分
          help[cnt++] = arr[p1++];
  
      while(p2 <= R)//这两个只会运行一个
          help[cnt++] = arr[p2++];
      
      for (int i = 0; i < cnt; i++)
          arr[L + i] = help[i];
  
  }
  ```

## 例题

### 小和问题

- 在一个数组中，每一个数左边比当前数小的数累加起来，叫做这个数组的小和，求一个数组的小和。
- 例子：[1，3，4，2，5], 1左边比1小的数，没有；3左边比3小的数字，1；4左边比4小的数字，1、3；2左边比2小的数字1；5左边比5小的数字，1、3、4、2所以这个数组的小和为1 + 1 + 3 +1 + 1 + 3 + 4 + 2 = 16 ;

- 分析：可以这样想右边**比1大**的有**4个**所以是**4 * 1**；右边**比3大**的**有2个**所以是**2  * 3**；右边**比4大**的**有1个**所以是**1 * 4**；**比2大**的**有1个**所以为**1 * 2**;比5大的没有，所以为4 * 1 + 2 * 3 + 1 * 4 + 1 * 2 = 16，所以可以在归并中是比较，由于归并是有序的则如果**arr[p1] < arr[p2]**那么**ans = ans  + arr[p1] * (R-p2+1)**，因为在归并是左右两边是**有序**的。所以可以得到答案

- ```c++
  //归并
  void merge(int arr[], int L, int mid, int R)
  {
      int help[R - L + 1];
      int cnt = 0;
      int p1 = L;
      int p2 = mid + 1;
  
      while(p1 <= mid && p2 <= R)
      {
          if (arr[p1] < arr[p2])
          {
              ans = ans + arr[p1] * (R - p2 + 1);
              help[cnt++] = arr[p1++];
          }
          if (arr[p1] >= arr[p2])//注意如果相等则要把右边的放到辅助数组里
              help[cnt++] = arr[p2++];
      }
  
      while(p1 <= mid)
          help[cnt++] = arr[p1++];
  
      while(p2 <= R)
          help[cnt++] = arr[p2++];
      
      for (int i = 0; i < cnt; i++)
          arr[L + i] = help[i];
  
  }
  ```

### 逆序对

- 如果左边的数比右边的大，就是一个逆序对，问有多少个逆序对。
- 只需要在merge中在比较时**如果左边的比右边的大那么ans = ans + mid - p1 + 1**（有序）在相等时先把左边的放到辅助数组里

- ```c++
  while(p1 <= mid && p2 <= R)
      {
          if (arr[p1] > arr[p2])
          {
              ans = ans + (mid - p1 + 1);
              help[cnt++] = arr[p2++];
          }
          if (arr[p1] <= arr[p2])//注意如果相等则要把右边的放到辅助数组里
              help[cnt++] = arr[p1++];
      }
  ```

## 荷兰国旗问题(快速排序)

- 问题1：

  给定一个数组arr，和一个数num，请把小于等于num的数放在数组的左边，大于num的数放在数组的右边。要求空间复杂度为O(1)，时间复杂度O(N);

- 分析:设小区等于区为j，指向数组的位置为i，然后比较arr[i]和num
  $$
  \begin{cases}
  swap(arr[j + 1], arr[i]);i++;j++; &arr[i] <= num
  \\i++; & arr[i] > num
  \end{cases}
  $$
  
- 相当于用**j**和**i**将数组arr**分成三个部分**，**0~j**为**小于等于num**的，**j + 1~i**为**大于num**的，**i~arr.length()-1为没遍历的部分**

- ```c++
  void fen()
  {
      int n, num, arr[100];
      cin >> n;
  
      for (int i = 1; i <= n; i++)
          cin >> arr[i];
      cin >> num;
      int i = 1;
      int j = 0;//小于等于区
      while(i <= n)
      {
          if (arr[i] <= num)//如果小于等于就和小于等于区的下一个交换，然后小于等于区往外扩一个，i++
          {
              swap(arr[i], arr[j + 1]);
              i++;
              j++;
          }
          else
              i++;
      }
      for (i = 1; i <= n; i++)
          cout << arr[i] << " ";
  }
  ```

- 问题2（荷兰国旗问题）

  给定一个数组arr，和一个数num，请把小于num的数放在数组的左边，等于num的数放在数组的中间，大于num的数放在数组的右边。要求额外空间复杂度O(1),时间复杂度O(N);

- 分析：无非就三种情况

  - 1> 当arr[i] < num；arr[i]和小于区的下一个交换，小于区右扩，i++
  - 2> 当arr[i] = num；i++
  - 3> 当arr[i] > num；arr[i]和大于区的前一个交换，大于区左扩，i不变(因为交换后该数还没判断所以不变)

- ```c++
  void fen1()
  {
      int n, num, arr[100];
      cin >> n;
  
      for (int i = 0; i < n; i++)
          cin >> arr[i];
      cin >> num;
      int i = 0;//数组位置
      int j = -1;//小于区
      int k = n;//大于区
      while(i < k)
      {
          if (arr[i] < num)//如果小于等于就和小于等于区的下一个交换，然后小于等于区往外扩一个，i++
          {
              swap(arr[i], arr[j + 1]);
              i++;
              j++;
          }
          else if (arr[i] > num)
          {
              swap(arr[i], arr[k - 1]);
              k--;
          }
          else
              i++;
          
      }
      for (i = 0; i < n; i++)
          cout << arr[i] << " ";
  }
  ```

## 快速排序

- 先选择数组**最右边的数字**使之成为**num**然后再将数组分为两个部分，**前面的部分是小于num的**，**右边的是大于num的**，然后再将最右边的数字和小于区的下一个位置进行交换，然后再**分别**对这**两个部分**进行**快速排序**。

- ```c++
  //快速排序
  void QuickSort(int arr[], int L, int R)
  {
      if (L < R)
      {
          int q = partition(arr, 0, R);
          QuickSort(arr, L, q - 1);
          QuickSort(arr, q + 1, R);
      }
  }
  
  //分割
  int partition(int arr[], int L, int R)
  {
      int less = L - 1;//<区右边界
      int more = R; //>区左边界
      while(L < more)
      {
          if (arr[L] < arr[R])
              swap(arr[++less], arr[L++]);
          else if (arr[L] > arr[R])
              swap(arr[L], arr[--more]);
          else
              L++;
      }
      swap(arr[more], arr[R]);
      return less + 1;
  
  }
  ```

