---
layout: post
title: "二叉树排序"
date: 2012-09-11 09:02
comments: true
categories: 算法
tags: 排序
---
唉，在公司实习都呆傻了，现在写起C++的程序都这么费劲。早上试着写了个二叉树排序都花了半个多小时，这要是半年前的我。。。    

```c++
#include <iostream>
using namespace std;
typedef struct Node
{
    int value;
    struct Node * left;
    struct Node * right;
}node;
node * root=new node;
void addToTree(node *current,int n)
{
    if(n >= current->value)
    {
        if(current->right==NULL)
        {
            node * r=new node;
            current->right=r;
            r->value=n;
            r->left=NULL;
            r->right=NULL;
        }
        else
        {
            addToTree(current->right, n);
        }
    }
    else
    {
        if(current->left==NULL)
        {
            node * l=new node;
            current->left=l;
            l->value=n;
            l->left=NULL;
            l->right=NULL;
        }
        else
        {
            addToTree(current->left, n);
        }
    }
}
 
void printTree(node * current)
{
    if(current==NULL)
    {
        return ;
    }
    else
    {
        printTree(current->left);
        cout<<current->value<<endl;
        printTree(current->right);
    }
}
 
int main()
{
    root->value=13;
    root->left=NULL;
    root->right=NULL;
    addToTree(root,31);
    addToTree(root,11);
    addToTree(root,61);
    addToTree(root,23);
    addToTree(root,8);
    addToTree(root,18);
    addToTree(root,4);
    addToTree(root,2);
    addToTree(root,9);
    addToTree(root,6);
    printTree(root);
}
```
