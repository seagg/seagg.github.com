---
layout: post
title: "RubyWarrior 一个人工智能小游戏"
date: 2013-02-27 18:24
comments: true
categories: Ruby
tags: [游戏]
---
项目主页：[http://github.com/ryanb/ruby-warrior/tree/master](http://github.com/ryanb/ruby-warrior/tree/master)    
这个游戏以一种有趣交互的方式来教你使用Ruby语言和人工智能。     
在游戏中你扮演一个勇士去攀登高塔得到顶层的珍贵的红宝石。     
在塔中每一层也就是每一关，你需要编写一个Ruby脚本来指导主角如何到达楼梯处然后进入下一关。     
在每一关你必须给足主角战斗的力量，你得考虑主角还有多少生命值，什么时候得休息恢复一下生命值等等。注意：每一个回合里，主角只能有一步操作，比如前进一次、休息加血、进攻、解救人质等。     
具体的安装，运行和规则请参考github上的项目主页。这里直接贴出beginner级别的第七关代码（前几关忘记保存了...）。    
这个游戏中每一关中的每一步都得小心翼翼，很有可能战斗过程中被干掉，所以每一步都得考虑好。比如这一关，地图是这样的：    

     ------
    |>a S @|
     ------

图中的`@`表示自己，`S`表示怪兽，`a`表示弓箭手，而目标就是干掉这两个敌人然后活着走到`>`处。    
每一关，人物默认都是向右走的，这里走一步发现撞墙了，所以要转向。走两步发现遇到怪兽了，要用`warrior.attack!`进行攻击。    
为了防止失血过多，当发现这一轮没有受到攻击且剩余血<20的时候，可以通过休息来补充血液。    
弓箭手与怪兽的一个不同就是弓箭手可以远程攻击，有可能你向前走一步，进入弓箭手的射程，但你还没走到他面前，这一过程一直要减血。所以为了防止到弓箭手面前的时候剩余血液不足以干掉他的情况发生，需要有个判断，就是当这一轮受到攻击，且血液不足以走到弓箭手面前将他干掉，需要后退几步，退出他的射程，然后休息补血，血液充足了再向前走。    

```c
Level 7

You feel a wall right in front of you and an opening behind you.

Tip: You are not as effective at attacking backward. Use warrior.feel.wall? and warrior.pivot! to turn around.

 ------
|>a S @|
 ------

  > = Stairs
  @ = xuhai (20 HP)
  a = Archer (7 HP)
  S = Thick Sludge (24 HP)


Warrior Abilities:

  warrior.pivot!
    Rotate :left, :right or :backward (default)

  warrior.rescue!
    Rescue a captive from his chains (earning 20 points) in given direction (forward by default).

  warrior.health
    Returns an integer representing your health.

  warrior.rest!
    Gain 10% of max health back, but do nothing more.

  warrior.feel
    Returns a Space for the given direction (forward by default).

  warrior.attack!
    Attacks a unit in given direction (forward by default).

  warrior.walk!
    Move in the given direction (forward by default).


When you're done editing player.rb, run the rubywarrior command again.
```

```ruby
class Player
  def play_turn(warrior)
    if warrior.feel.wall?
        warrior.pivot!
    else
       if warrior.feel.enemy?
           warrior.attack!
       else
           if warrior.health < 20 && (warrior.health == @health || warrior.health == @health +2)
               warrior.rest!
           else
               if warrior.health <15 && (warrior.health < @health )
                   warrior.walk!(:backward)
               else
                   warrior.walk!
               end
           end
        end
    end
    @health = warrior.health
  end
end
```

```c
每轮战况!
- turn 1 -
 ------
|>a S @|
 ------
xuhai pivots backward
- turn 2 -
 ------
|>a S @|
 ------
xuhai walks forward
- turn 3 -
 ------
|>a S@ |
 ------
xuhai attacks forward and hits Thick Sludge
Thick Sludge takes 5 damage, 19 health power left
Thick Sludge attacks forward and hits xuhai
xuhai takes 3 damage, 17 health power left
- turn 4 -
 ------
|>a S@ |
 ------
xuhai attacks forward and hits Thick Sludge
Thick Sludge takes 5 damage, 14 health power left
Thick Sludge attacks forward and hits xuhai
xuhai takes 3 damage, 14 health power left
- turn 5 -
 ------
|>a S@ |
 ------
xuhai attacks forward and hits Thick Sludge
Thick Sludge takes 5 damage, 9 health power left
Thick Sludge attacks forward and hits xuhai
xuhai takes 3 damage, 11 health power left
- turn 6 -
 ------
|>a S@ |
 ------
xuhai attacks forward and hits Thick Sludge
Thick Sludge takes 5 damage, 4 health power left
Thick Sludge attacks forward and hits xuhai
xuhai takes 3 damage, 8 health power left
- turn 7 -
 ------
|>a S@ |
 ------
xuhai attacks forward and hits Thick Sludge
Thick Sludge takes 5 damage, -1 health power left
Thick Sludge dies
xuhai earns 24 points
- turn 8 -
 ------
|>a  @ |
 ------
xuhai receives 2 health from resting, up to 10 health
Archer shoots forward and hits xuhai
xuhai takes 3 damage, 7 health power left
- turn 9 -
 ------
|>a  @ |
 ------
xuhai walks backward
Archer shoots and hits nothing
- turn 10 -
 ------
|>a   @|
 ------
xuhai receives 2 health from resting, up to 9 health
- turn 11 -
 ------
|>a   @|
 ------
xuhai receives 2 health from resting, up to 11 health
- turn 12 -
 ------
|>a   @|
 ------
xuhai receives 2 health from resting, up to 13 health
- turn 13 -
 ------
|>a   @|
 ------
xuhai receives 2 health from resting, up to 15 health
- turn 14 -
 ------
|>a   @|
 ------
xuhai receives 2 health from resting, up to 17 health
- turn 15 -
 ------
|>a   @|
 ------
xuhai receives 2 health from resting, up to 19 health
- turn 16 -
 ------
|>a   @|
 ------
xuhai receives 1 health from resting, up to 20 health
- turn 17 -
 ------
|>a   @|
 ------
xuhai walks forward
- turn 18 -
 ------
|>a  @ |
 ------
xuhai walks forward
Archer shoots forward and hits xuhai
xuhai takes 3 damage, 17 health power left
- turn 19 -
 ------
|>a @  |
 ------
xuhai walks forward
Archer shoots forward and hits xuhai
xuhai takes 3 damage, 14 health power left
- turn 20 -
 ------
|>a@   |
 ------
xuhai attacks forward and hits Archer
Archer takes 5 damage, 2 health power left
Archer shoots forward and hits xuhai
xuhai takes 3 damage, 11 health power left
- turn 21 -
 ------
|>a@   |
 ------
xuhai attacks forward and hits Archer
Archer takes 5 damage, -3 health power left
Archer dies
xuhai earns 7 points
- turn 22 -
 ------
|> @   |
 ------
xuhai receives 2 health from resting, up to 13 health
- turn 23 -
 ------
|> @   |
 ------
xuhai receives 2 health from resting, up to 15 health
- turn 24 -
 ------
|> @   |
 ------
xuhai receives 2 health from resting, up to 17 health
- turn 25 -
 ------
|> @   |
 ------
xuhai receives 2 health from resting, up to 19 health
- turn 26 -
 ------
|> @   |
 ------
xuhai receives 1 health from resting, up to 20 health
- turn 27 -
 ------
|> @   |
 ------
xuhai walks forward
- turn 28 -
 ------
|>@    |
 ------
xuhai walks forward
Success! You have found the stairs.
Level Score: 31
Time Bonus: 2
Clear Bonus: 7
Total Score: 364 + 40 = 404
```
很不错的游戏，可惜没有Java或者C版的，有空考虑写一个。当然，要是能写成对战类的更好了。    
今天在github上溜达的时候发现有个哥们的[第六关代码](https://github.com/peterwongpp/PeterWarrior)，比较牛，不想我傻呵呵的一直用if分支。有空学习学习！
