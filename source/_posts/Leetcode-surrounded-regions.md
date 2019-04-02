---
title: Leetcode-surrounded-regions
date: 2019-03-30 14:55:08
categories: 算法
tags:
- Leetcode
- Java
- Array
---
题目描述

Given a 2D board containing'X'and'O', capture all regions surrounded by'X'.

A region is captured by flipping all'O's into'X's in that surrounded region .

For example,

	X X X X
	X O O X
	X X O X
	X O X X

After running your function, the board should be:

	X X X X
	X X X X
	X X X X
	X O X X
<!--more-->
---
这一题我们可以通过标记来解决，如果一个O点不能通过它自己或它的邻居接触刀边界，那么它就被包围了，我们可以先标记没有被包围的）点，剩下的就是被包围的。

举例：

	X X X X
	X X O X
	X O X X
	X O X X

对于这样一个二维数组，我们遍历边界，将边界上的O变成T

	X X X X
	X X O X
	X O X X
	X T X X

然后将T附近的O也变成T，重复操作直到没有O点再需要变化

	X X X X
	X X O X
	X T X X
	X T X X

然后将所有的O变成X

	X X X X
	X X X X
	X T X X
	X T X X

将所有的T变回O

	X X X X
	X X X X
	X O X X
	X O X X

这就是我们要的结果

```java
public class Solution {

    public void solve(char[][] board) {
        
        if (board.length <= 1 || board[0].length <= 1){
            return;
        }

        int row = board.length;
        int col = board[0].length;

        for (int i = 0; i < row; i++){
            if (board[i][0] == 'O'){
                target(i, 0, board);
            }
            if (board[i][col-1] == 'O'){
                target(i, col-1, board);
            }
        }

        for (int j = 0; j < col; j++){
            if(board[0][j] == 'O'){
                target(0, j, board);
            }
            if(board[row-1][j] == 'O'){
                target(row-1, j, board);
            }
        }

        for (int i = 0; i < row; i++){
            for (int j = 0; j < col; j++){
                if (board[i][j] == 'O'){
                    board[i][j] = 'X';
                }else if (board[i][j] == 'T'){
                    board[i][j] = 'O';
                }
            }
        }

    }

    private void target(int x, int y, char[][] board) {
        int row = board.length;
        int col = board[0].length;

        board[x][y] = 'T';

        if(x > 0 && board[x-1][y] == 'O'){
            target(x-1, y, board);
        }
        if(x < row-1 && board[x+1][y] == 'O'){
            target(x+1, y, board);
        }
        if(y > 0 && board[x][y-1] == 'O'){
            target(x, y-1, board);
        }
        if(y < col-1 && board[x][y+1] == 'O'){
            target(x, y+1, board);
        }
    }
}
```
