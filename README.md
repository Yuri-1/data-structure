# python
# Queue 
# 打印机任务 队列结构 python实现





from pythonds.basic.queue import Queue
import random
class Priter:
    def __init__(self,ppm):
        self.pagerate = ppm  #打印速度
        self.currentTask = None  #打印任务
        self.timeRemaining = 0  #任务倒计时

    def tick(self):   #打印1秒
        if self.currentTask != None:
            self.timeRemaining = self.timeRemaining - 1
            if self.timeRemaining <= 0:
                self.currentTask = None

    def busy(self):  #打印是否忙
        if self.currentTask != None:
            return True
        else:
            return False

    def startNext(self,newtask):   #打印新作业
        self.currentTask = newtask
        self.timeRemaining = newtask.getPages()*60/self.pagerate

class Task:
    def __init__(self,time):
        self.timestamp = time   #生成时间戳
        self.page = random.randrange(1,21)   #打印页数

    def getStamp(self):
        return self.timestamp

    def getPages(self):
        return self.page

    def waitTine(self,currenttime):
        return currenttime - self.timestamp    #等待时间

def newPrintTask():            #模拟1/180概率生成作业
    num = random.randrange(1,181)
    if num == 180:        #任意一个数即可，关键要模拟概率
        return True
    else:
        return False

def simulation(numSeconds,pagesPerMinute):   #主体——模拟 两个参数：打印多长时间（秒） 打印机设定模式
    # 1 准备工作
    labprinter = Printer(pagesPerMinute) #生成一个打印机对象
    printQueue = Queue()   #准备打印队列
    waitingtimes = []   #生成等待时间列表，以便最后计算平均等待时间

    # 2 主体——时间流逝  迭代循环
    for currentSecond in range(numSeconds):
        if newPrintTask():      #s1 生成打印作业的步骤
            task = Task(currentSecond)
            printQueue.enqueue(task)

        if (not labprinter.busy()) and (not printQueue.isEmpty()):   #s2 判断打印机状态：不忙且有作业进来
            nexttask = printQueue.dequeue()   #将作业中的打印任务取出来，变为下一个
            waitingtimes.append(nexttask.waitTime(currentSecond))   #计算等待时间并加入到列表中
            labprinter.startNext(nexttask)   #开始打印
        
        labprinter.tick()   #打印1秒

    # 3 平均等待时间，并输出还剩几个没有打印
    averageWait = sum(waitingtimes)/len(waitingtimes)
    print('Average Wait %6.2f secs %3d tasks remaining.'%(averageWait,printQueue.size()))
