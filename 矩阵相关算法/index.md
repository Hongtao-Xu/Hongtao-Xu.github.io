# 矩阵相关算法


<!--more-->

# 1.求矩阵中子矩阵的最大值

> Leetcode原题：

[面试题 17.24. 最大子矩阵 - 力扣（LeetCode）](https://leetcode.cn/problems/max-submatrix-lcci/)

就是求解一个矩阵中，所有子矩阵的最大值(子矩阵元素之和)，返回子矩阵的左上和右下坐标值

```java
class Solution {
    public int[] getMaxMatrix(int[][] matrix) {
        int[] ans = new int[4];//保存最大子矩阵的左上角和右下角的行列坐标
        //N:行，M:列
        int N = matrix.length, M = matrix[0].length;
        int sum;
        int maxSum = Integer.MIN_VALUE;
        int startX = -1, startY = -1;
        for (int i = 0; i < N; i++) {//i为上界，从上往下扫描
            int[] b = new int[M];//记录当前i~j行组成大矩阵的每一列的和
            for (int j = i; j < N; j++) {//子矩阵的下界，从i~N-1
                sum = 0;
                for (int k = 0; k < M; k++) {//遍历所有列
                    b[k] += matrix[j][k];
                    if (sum > 0) {
                        sum += b[k];
                    } else {
                        sum = b[k];//<0,舍弃之前的,重新计算sum
                        startX = i;
                        startY = k;
                    }
                    if (sum >= maxSum) {
                        maxSum = sum;
                        ans[0] = startX;//更新最终结果
                        ans[1] = startY;//更新最终结果
                        ans[2] = j;//更新最终结果
                        ans[3] = k;//更新最终结果
                    }
                }

            }
        }
        return ans;
    }
}
```

# 2.求子矩阵中正整数子矩阵的最大值

> 这一类型的主要特点是：所求的子矩阵的元素类型是有要求的

## 2.1 子矩阵(1)的最大值

[剑指 Offer II 040. 矩阵中最大的矩形 - 力扣（LeetCode）](https://leetcode.cn/problems/PLYXKQ/)

![image-20221004211651936](https://cdn.staticaly.com/gh/Hongtao-Xu/note@main/img/202210042116008.png)

```text
子矩阵必须是1组成的

输入：matrix = ["10100","10111","11111","10010"]
输出：6
解释：最大矩形如上图所示。
```



```java
class Solution {
    public int maximalRectangle(String[] matrix) {
        //特判
        int m = matrix.length;
        if (m == 0) {
            return 0;
        }
        int n = matrix[0].length();
        //此元素左边的1的个数
        int[][] left = new int[m][n];

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i].charAt(j) == '1') {
                    //填充left
                    left[i][j] = (j == 0 ? 0 : left[i][j - 1]) + 1;
                }
            }
        }

        int ret = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                //不符合，跳过
                if (matrix[i].charAt(j) == '0') {
                    continue;
                }
                //width=1的长度
                int width = left[i][j];
                int area = width;//area=wide*1;
                for (int k = i - 1; k >= 0; k--) {
                    //只能依最小的作为子矩阵的宽
                    width = Math.min(width, left[k][j]);
                    area = Math.max(area, (i - k + 1) * width);
                }
                ret = Math.max(ret, area);
            }
        }
        return ret;
    }
}
```

## 2.1 子矩阵(非0元素)的最大值

> 这种类型的特点是，子矩阵只能由非0元素组成

![image-20221004212614899](https://cdn.staticaly.com/gh/Hongtao-Xu/note@main/img/202210042126943.png)

如上图，最大子矩阵为标红部分，最大值为：26

```java
/*
 * 矩阵中的子矩形最大值
 */
public class Main1 {
    static int[][] arr;
    static int m, n;

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        m = sc.nextInt();
        n = sc.nextInt();
        arr = new int[m][n];
        sc.nextLine();
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                arr[i][j] = sc.nextInt();
            }
        }
        //============逻辑代码==============
        //特判：
        if (m == 0) {
            System.out.println(0);
            return;
        }
        //(i,j)元素的左边非0元素的个数，包括这个元素
        int[][] left = new int[m][n];

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (arr[i][j] != 0) {
                    left[i][j] = (j == 0 ? 0 : left[i][j - 1]) + 1;
                }
            }
        }

        //转化为一系列的柱状图
        int ret = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                //以arr[i][j]为右下角的矩阵
                if (arr[i][j] == 0) {
                    continue;
                }
                int width = left[i][j];
                int area = getArea(i, j, width, 1);//一行的面积
                for (int k = i - 1; k >= 0; k--) {//往上遍历
                    width = Math.min(width, left[k][j]);//只能依最短的宽度
                    area = Math.max(area, getArea(i, j, width, (i - k + 1)));
                }
                ret = Math.max(ret, area);
            }
        }
        System.out.println(ret);
    }

    public static int getArea(int x, int y, int kuan, int gao) {
        int sum = 0;
        //求一个矩形里数字的和
        for (int i = x - gao + 1; i <= x; i++) {
            for (int j = y - kuan + 1; j <= y; j++) {
                sum += arr[i][j];
            }
        }
        return sum;
    }
}
```


