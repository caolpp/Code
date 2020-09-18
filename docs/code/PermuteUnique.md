# 全排列

给定一个可包含重复数字的序列，返回所有不重复的全排列。

# 递归 + 回溯 + 剪枝

剪枝主要对于使用过的元素的理解，定义一个 `visited` 数组，用来记录数组元素的访问状态。

```code
    visited[i - 1] == true，说明同一树支nums[i - 1]使用过
     visited[i - 1] == false，说明同一树层nums[i - 1]使用过
    如果同一树层nums[i - 1]使用过则直接跳过, 即 !visited[i-1]
```

```java
import java.util.*;

public class PermuteUnique {
    /**
     * 给定一个可包含重复数字的序列，返回所有不重复的全排列。
     *
     * 示例:
     *
     * 输入: [1,1,2]
     * 输出:
     * [
     *   [1,1,2],
     *   [1,2,1],
     *   [2,1,1]
     * ]
     * @param nums
     * @return
     */
    List<List<Integer>> result = new ArrayList<>();
    boolean[] visited;
    public List<List<Integer>> permuteUnique(int[] nums) {
        if(nums.length <= 0){
            return result;
        }
        visited = new boolean[nums.length];
        Arrays.sort(nums);
        backtracking(nums, 0, new ArrayList<>());
        return result;
    }

    private void backtracking(int[] nums, int index, List<Integer> tempLinkedList) {
        if(index == nums.length){
            result.add(new ArrayList<>(tempLinkedList));
            return;
        }
        for (int i = 0; i < nums.length; i++){
            // visited[i - 1] == true，说明同一树支nums[i - 1]使用过
            // visited[i - 1] == false，说明同一树层nums[i - 1]使用过
            // 如果同一树层nums[i - 1]使用过则直接跳过, 即 !visited[i-1]
            if(visited[i]){
                continue;
            }
            if(i > 0 && nums[i] == nums[i-1] && !visited[i-1]){
                continue;
            }
            tempLinkedList.add(nums[i]);
            visited[i] = true;
            backtracking(nums, index + 1, tempLinkedList);
            visited[i] = false;
            tempLinkedList.remove(tempLinkedList.size()-1);
        }

    }

    public static void main(String[] args) {
        PermuteUnique permuteUnique = new PermuteUnique();
        int[] nums = {1,1,2};
        List<List<Integer>> resultList = permuteUnique.permuteUnique(nums);
        for(List<Integer> list : resultList){
            for(int num : list){
                System.out.print(num + " ");
            }
            System.out.println("\r\n");
        }
    }
}

```