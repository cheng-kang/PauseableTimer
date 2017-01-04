# PauseableTimer
**Pauseable Timer** is a pause-able timer written in Swift.

There are occassions when we need timers to pause, however, this feature is not implemented in Timer(Swift3). Thus, after some failed attempts, I create this pause-able timer which hopefully could be useful to you.

## How to Use
1. Create a Timer

	```
	e.g.
	let timer = Timer.scheduledTimer(timeInterval: 10, target: self, selector: aSelector, userInfo: nil, repeats: true)
	```

2. Create a Pauseable Timer and pass the timer as an argument.

	```
	e.g.
	let pauseableTimer = PauseableTimer(timer: timer)
	```

3. Pause and Resume!

	```
	e.g.
	pauseableTimer.pause()
	pauseableTimer.resume()
	```

**Notice**

- If the timer you use to initialize a PauseableTimer is not scheduled (or added to RunLoop) already, you need to manually schedule it (ro add it to RunLoop). You can do it by `RunLoop.current.add(timer, forMode: .commonModes)`. 
- Use `timer` property of PauseableTimer if you need to manipulate the timer or get it’s properties. Like this `pauseableTimer.timer.firedate = Date()`.
- A convinient method `invalidate()` is created to invalidate the timer.

## Intro
Pauseable Timer does not re-implement Timer in Swift, but takes a Timer as a property and implement 'Pause' feature with the help of it.

The concept of this feature is inspired by [NSTimer 总结1(包括计时器不准的解决)](http://www.jianshu.com/p/e554a164d0da). 

The main idea is:
> set the firedate of Timer to a un-reachable date when you want it pause (the timer will wait until forever and you can take it as pausing :D), and set it back to its original firedate when you want it resume (and the timer will fire "as expected").

Simple and Excellent!

You might have noticed the double qouted "as expected". Acctually, this might fail your expectation because

1.  The firedate is expried.

	```
e.g.
The current time is 2017.01.04 10:25.
The original firedate is 2017.01.04 10:30.
You pause the timer at this moment and resume it 10 mins after which is 2017.01.04 10:35. So the timer has missed it's expected firedate. 
However it will fire once you set the firedate back to 2017.01.04 10:30 at 2017.01.04 10:35, if the timer is already added to RunLoop.
```

- The timer is expected to wait for the remaining waiting time.

	```
e.g.
The current time is 2017.01.04 10:25:00.
The timeInterval of the timer is 60 seconds. It's scheduled at this moment, and should fire 60 seconds later at 2017.01.04 10:26:00.
And for some reason you pause it 10 seconds after, which is 2017.01.04 10:25:10. And you resume the timer at 2017.01.04 10:25:50.
However, you still want it to fire 60 seconds after it's scheduled, regardless the pausing part. So it should wait for 50 seconds more and fire at 2017.01.04 10:26:40.
```

An ideal solution should be one that fits all 3 conditions (another one is the condition that someone doesn’t care if the original firedate is expired or not) discussed above. However, this project only deals with condition 2 because that is the problem I encountered with.

Hopefully, I will continue this small project in the future and complete it with solutions to every condition.
