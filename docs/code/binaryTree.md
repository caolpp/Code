# 二叉树的构建与打印

## 二叉树的构建

输入为一个数组 *nums*，其中节点为空用 *null* 表示

首先将数组进行整型化，将数组中的数字逐个转换为整型数字，如果为空那么使用 *Integer.MAX_VALUE* 存入数组

构建一个 *TreeNode* 类型的数组 *treeNodes*，数组长度与输入数组长度一致

将输入数组 *nums* 的值放入到 *treeNodes*， 如果 *nums[i]* 的值为 *nteger.MAX_VALUE*，则该节点为空

通过子节点和父节点的关系，进行节点连接，此处需要判断 *i x 2 + 1* 和 *i x 2 + 2* 与 *nums.length* 的关系（小于），同时循环的次数应该小于 *treeNodes.length / 2-1*

返回 *treeNodes[0]*

```java
node.left = treeNodes[i * 2 + 1]
node.right = treeNodes[i * 2 + 2];
```

构建完整代码实现
```java
public class CreateTree {
    public int[] strToNum(String[] numsArr){
        if(numsArr.length <= 0){
            return null;
        }
        int[] nums = new int[numsArr.length];
        for (int i = 0; i < numsArr.length; i++){
            if("null".equals(numsArr[i].toLowerCase())){
                nums[i] = Integer.MAX_VALUE;
            }else{
                nums[i] = Integer.parseInt(numsArr[i]);
            }
        }
        return nums;
    }

    public TreeNode createTree(String[] numsArr){
        int[] nums = strToNum(numsArr);
        if(nums.length <= 0){
            return null;
        }
        TreeNode[] treeNodes = new TreeNode[nums.length];
        for(int i = 0; i < nums.length; i++){
            if(nums[i] != Integer.MAX_VALUE){
                treeNodes[i] = new TreeNode(nums[i]);
            }else {
                treeNodes[i] = null;
            }
        }
        TreeNode root;
        for(int i = 0; i <= treeNodes.length / 2-1; i++){
            root = treeNodes[i];
            if(treeNodes[i] != null){
                if(i * 2 + 1 < treeNodes.length){
                    root.left = treeNodes[i * 2 + 1];
                }
               if( i * 2 + 2 < treeNodes.length){
                   root.right = treeNodes[i * 2 + 2];
               }
            }
        }
        return treeNodes[0];
    }

    public static void main(String[] args) {
        CreateTree createTree = new CreateTree();
        String[] nums = {"1","2","3","4","5","6","7","null","8"};
        TreeNode node = createTree.createTree(nums);
        PrintTree printTree = new PrintTree();
        printTree.show(node);
    }
}
```

为了更加直观的看出二叉树的样式，所以将二叉树采用树形结构进行打印

主要思想是将二叉树节点的值，*/*， *\\* 存入到一个扩展数组中，然后将其打印

假设树的高度为 *treeDepth*，那么最后一行的宽度为 *2 << (treeDepth - 2)) x 3 + 1*，
数组的宽度为最后一行的宽度，高度为 *treeDepth x 2 - 1*

采用递归的方式将值与符号写入数组

```伪代码
    dfs(curNode, rowIndex, columnIndex, res, treeDepth);
    // 结束条件有两个，1. 当前节点为空，2. 当前层 = 树的深度;
    if(currNode == null){
        return;
    }
    将 currNode.val 写入 res[rowIndex][columnIndex]中;
    当前节点层 = (rowIndex + 1) / 2
    if(当前层 == 树的深度){
        return;
    }
    计算符号-数字，数字-符号之间列差距gap;
    if(左孩子不为空){
        将 '/' 写入数组 res[rowIndex + 1][columnIndex - gap] = "/";
        递归处理左子树；
        dfs(root.left, rowIndex + 2, columnIndex-gap*2, res, treedepth);
    }
    if(右孩子不为空){
        将 '\' 写入数组 res[rowIndex + 1][columnIndex + gap] = "/";
        递归处理左子树；
        dfs(root.right, rowIndex + 2, columnIndex+gap*2, res, treedepth);
    }
```


## 二叉树的打印

打印代码完整实现
```java
public class PrintTree {
    public static int getTreeDepth(TreeNode root) {
        return root == null ? 0 : (1 + Math.max(getTreeDepth(root.left), getTreeDepth(root.right)));
    }

    private static void writeArray(TreeNode currNode, int rowIndex, int columnIndex, String[][] res, int treeDepth) {
        // 保证输入的树不为空
        if (currNode == null) return;
        // 先将当前节点保存到二维数组中
        res[rowIndex][columnIndex] = String.valueOf(currNode.val);

        // 计算当前位于树的第几层
        int currLevel = ((rowIndex + 1) / 2);
        // 若到了最后一层，则返回
        if (currLevel == treeDepth) return;
        // 计算当前行到下一行，每个元素之间的间隔（下一行的列索引与当前元素的列索引之间的间隔）
        int gap = treeDepth - currLevel - 1;

        // 对左儿子进行判断，若有左儿子，则记录相应的"/"与左儿子的值
        if (currNode.left != null) {
            res[rowIndex + 1][columnIndex - gap] = "/";
            writeArray(currNode.left, rowIndex + 2, columnIndex - gap * 2, res, treeDepth);
        }

        // 对右儿子进行判断，若有右儿子，则记录相应的"\"与右儿子的值
        if (currNode.right != null) {
            res[rowIndex + 1][columnIndex + gap] = "\\";
            writeArray(currNode.right, rowIndex + 2, columnIndex + gap * 2, res, treeDepth);
        }
    }

    public void show(TreeNode root) {
        if (root == null) System.out.println("EMPTY!");
        // 得到树的深度
        int treeDepth = getTreeDepth(root);

        // 最后一行的宽度为2的（n - 1）次方乘3，再加1
        // 作为整个二维数组的宽度
        int arrayHeight = treeDepth * 2 - 1;
        int arrayWidth = (2 << (treeDepth - 2)) * 3 + 1;
        // 用一个字符串数组来存储每个位置应显示的元素
        String[][] res = new String[arrayHeight][arrayWidth];
        // 对数组进行初始化，默认为一个空格
        for (int i = 0; i < arrayHeight; i ++) {
            for (int j = 0; j < arrayWidth; j ++) {
                res[i][j] = " ";
            }
        }

        // 从根节点开始，递归处理整个树
        // res[0][(arrayWidth + 1)/ 2] = (char)(root.val + '0');
        writeArray(root, 0, arrayWidth/ 2, res, treeDepth);

        // 此时，已经将所有需要显示的元素储存到了二维数组中，将其拼接并打印即可
        for (String[] line: res) {
            StringBuilder sb = new StringBuilder();
            for (int i = 0; i < line.length; i ++) {
                sb.append(line[i]);
                if (line[i].length() > 1 && i <= line.length - 1) {
                    i += line[i].length() > 4 ? 2: line[i].length() - 1;
                }
            }
            System.out.println(sb.toString());
        }
    }

}
```

## 测试样例

```打印输入与输出样例
nums = {"1","2","3","4","5","6","7","null","8","9","10","11"}

            1            
         /     \         
      2           3      
    /   \       /   \    
  4       5   6       7  
   \                     
    8    

```

## 总结与完善
- [x] *二叉树的构建*
- [x] *二叉树的打印*
- [ ] *当数字或者字符长度超过一定数量时，图形显示不准确*