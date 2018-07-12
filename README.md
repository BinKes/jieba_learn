jieba
========
一.类
============
特点
========
      * 类属性、实例属性、类方法、实例方法以及静态方法
          1,类属性就是类对象所拥有的属性,实例属性是不需要在类中显示定义的
          2,私有属性:__name,类似于这种的称作私有属性，不能通过实例对象访问，只能在类的内部访问
          3,类方法：是类对象所拥有的方法，需要用修饰器"@classmethod"来标识其为类方法对于类方法，第一个参数必须是类对象，一般以"cls"作为第一个参数
              能够通过实例对象和类对象去访问,如下面的例子，类方法还有一个用途就是可以对类属性进行修改
          4,静态方法：需要通过修饰器"@staticmethod"来进行修饰，静态方法不需要多定义参数。
              普通方法不能通过类名调用，但是静态方法和类方法是可以的
              普通方法,可以通过self访问实例属性
              类方法,可以通过cls访问类属性,静态方法不能
              静态方法没有cls参数，所以它既不能访问实例变量，也不能访问类变量

例子
-----------------
      # 创建一个简单的类,来认识类
      class Dog():
          """创建Dog类"""
          # 构造方法，创建类的实例时会自动调用这个方法
          def __init__(self, name, age):
              # 以self 为前缀的变量都可供类中的所有方法使用
              self.name = name
              self.age = age
              self.heavy = 0  # 可以给属性指定初始值
          # 创建类的普通方法，普通方法的参数列表要有self参数
          def sit(self):
              print("小狗蹲下")

          def roll(self):
              print("dog is rolling")

          # 添加修改属性值得方法,修改体重
          def alter_attribute(self, heavy):
              self.heavy = heavy

          # 创建方法对属性值递增,对年龄进行递增
          def update_age(self, age):
              self.age += age


      # 创建dog的实例，并且传入参数
      my_dog = Dog("tom", 12)
      print("my dog's name is " + my_dog.name.title())
      # 调用类的方法
      my_dog.sit()
      my_dog.roll()
      '''
      修改属性值
      1，直接修改
      2，通过在类里面定义方法修改
      3，通过方法对属性值递增
      '''
      # 直接修改属性值,修改了my_dog这个对象的heavy属性
      print(my_dog.heavy)
      my_dog.heavy = 20
      print(my_dog.heavy)
      # 通过在类里面添加方法修改，然后直接调用方法对对象的属性进行修改，修改体重
      print("-" * 30)
      my_dog.alter_attribute(40)
      print(my_dog.heavy)
      # 通过方法对属性值递增,对年龄进行递增
      print(my_dog.age)
      my_dog.update_age(2)
      print("年龄增加后：" + str(my_dog.age))

      #类方法
      class people:
          country = 'china'  
          #类方法，用classmethod来进行修饰
          @classmethod
          def getCountry(cls):
              return cls.country

      p = people()
      print (p.getCountry())    #可以用过实例对象引用
      print (people.getCountry())    #可以通过类对象引用

      #静态方法
      class people:
          country = 'china'
          @staticmethod
          #静态方法，没有self和cls
          def getCountry():
              return people.country

      print (people.getCountry())

二.类的继承
==============
      一个类继承 另一个类时，它将自动获得另一个类的所有属性和方法；原有的类称为父类 ，
      而新类称为子类 。子类继承了其父类的所有属性和方法，同时还可以定义自己的属性和方法
=======
特点
-----------------
    1,创建子类的实例时，Python首先需要完成的任务是给父类的所有属性赋值
        通过子类的__init__函数里面使用super(子类名,self).__init__(参数表)来为父类属性赋值
    2,给子类定义属性和方法
        让一个类继承另一个类后，可添加区分子类和父类所需的新属性和方法
        子类也能在__init__函数中定义自己的属性
    3,重写父类的方法，可在子类中定义一个这样的方法，即它与要重写的父类方法同名
    4,将实例用作属性,就是实例化一个类，并且将这个类当属参数传到另外一个类
    5,多重继承,　在定义一个类的时候，可以在类名后面紧跟一对括号，在括号中指定所继承的父类，如果有多个父类，多个父类名之间用逗号隔开

例子
-----------------
      # 首先创建一个父类，汽车类
      class Car():
          """父类，汽车类"""

          # 创建init方法
          def __init__(self, make, model, year):
              self.make = make
              self.model = model
              self.year = year
              self.meter = 0  # 累计行驶距离

          def car_describe(self):
              des = "car的制造商：" + self.make + " car的品牌：" + self.model + " car的生产日期：" + str(self.year)
              return des

          def read_meter(self):
              print("This car has " + str(self.meter) + " miles on it.")

          def fill_gas_tank():
              """电动汽车没有油箱"""
              print("给汽车加油")


      # 创建子类，电动车
      class ElectricCar(Car):
          """电动汽车的独特之处"""

          def __init__(self, make, model, year, c_type):
              """初始化父类的属性"""
              super(ElectricCar, self).__init__(make, model, year)
              self.c_type = c_type #定义子类自己的属性
              self.electric_quantity = 3000

          #定义子类自己的方法：充电
          def charge(self,electric_quantity):
              if self.electric_quantity < 10000:
                  self.electric_quantity += electric_quantity
              else:
                  print("电量已满")

          def run(self):
              if self.electric_quantity >= 1000:
                  self.electric_quantity -=1000
              else:
                  print("电量不足,请充电")
          #重写父类方法
          def fill_gas_tank():
              print("电动车不用加油")
        
      # 实例化子类
      my_tesla = ElectricCar('tesla', 'model s', 2018, "ElectricCar")
      print(my_tesla.car_describe())
      #调用子类自己方法
      my_tesla.charge(2000)
      print(my_tesla.electric_quantity)
 

三. Python内置属性
----------------
          在Python中有一些内置的方法，这些方法命名都有比较特殊的地方（其方法名以2个下划线开始然后以
          2个下划线结束）。类中最常用的就是构造方法和析构方法。
              1,构造方法__init__(self,....)：在生成对象时调用，可以用来进行一些初始化操作，不需要显示去调用，系统会默认去执行
              2,析构方法__del__(self)：在释放对象时调用，支持重载，可以在里面进行一些释放资源的操作，不需要显示调用
              3,__new__():__new__()在__init__()之前被调用，用于生成实例对象。利用这个方法和类属
                  性的特性可以实现设计模式中的单例模式。单例模式是指创建唯一对象吗，单例模式设计的类只能实例化一个对象。
              4,
          Python类的内置的属性
              类的特殊属性
              C.__name__      类Ｃ的名字（字符串）
              C.__doc__       类Ｃ的文档字符串
              C.__bases__     类Ｃ的所有父类构成的元组
              C.__dict__      类Ｃ的属性
              C.__module__    类Ｃ定义所在的模块（1.5 版本新增）
              C.__class__     实例Ｃ对应的类（仅新式类中）

