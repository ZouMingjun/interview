# 单例模式
　　单例大约有两种实现方法：懒汉与饿汉。

懒汉：故名思义，不到万不得已就不会去实例化类，也就是说在第一次用到类实例的时候才会去实例化，所以上边的经典方法被归为懒汉实现；
饿汉：饿了肯定要饥不择食。所以在单例类定义的时候就进行实例化。
　　特点与选择：

由于要进行线程同步，所以在访问量比较大，或者可能访问的线程比较多时，采用饿汉实现，可以实现更好的性能。这是以空间换时间。
在访问量较小时，采用懒汉实现。这是以时间换空间。
```cpp
// 懒汉式单例模式
class Singleton
{
private:
	Singleton() { }
	static Singleton * pInstance;
public:
	static Singleton * GetInstance()
	{
		if (pInstance == nullptr)
			pInstance = new Singleton();
			//有括号的，就调用对应的构造函数。
			//没有加括号，如果有自定义的不带参数的构造函数，则调用该构造函数；
			//如果没有定义构造函数，就调用默认的不带参数的构造函数。
		return pInstance;
	}
};

// 线程安全的单例模式
//1、属于内部静态变量的懒汉经典模式,在instance函数里定义一个静态的实例，也可以保证拥有唯一实例，在返回时只需要返回其指针就可以。
class Singleton
{
private:
	Singleton() { }
	~Singleton() { }
	Singleton(const Singleton &);
	Singleton & operator = (const Singleton &);
public:
	static Singleton & GetInstance()
	{
		static Singleton instance;
		return instance;
	}
};
//2、加锁的懒汉模式
class singleton
{
protected:
    singleton()
    {
        pthread_mutex_init(&mutex);//构造函数，private类型也可以
    }
private:
    static singleton* p;
public:
    static pthread_mutex_t mutex;
    static singleton* initance();
};
pthread_mutex_t singleton::mutex;//静态成员变量只能在类外初始化
singleton* singleton::p = NULL;
singleton* singleton::initance()//静态成员函数类内定义也可以
{
    if (p == NULL)
    {
        pthread_mutex_lock(&mutex);
        if (p == NULL)
            p = new singleton();
        pthread_mutex_unlock(&mutex);
    }
    return p;
}
//饿汉模式
class singleton
{
protected:
    singleton()
    {}
private:
    static singleton* p;
public:
    static singleton* initance();
};
singleton* singleton::p = new singleton;
singleton* singleton::initance()
{
    return p;
}
```
在饿汉模式下，在单例类定义的时候就已经定义了一个对象，对类进行了初始化。后面不管哪个线程调用成员函数initance()，都只不过是返回一个对象的指针而已。所以是线程安全的，不需要在成员函数initance中加锁。

饿汉模式的实现是有隐患的，因为c++中对全局对象的构造顺序并没有明确的规定。假如有一个全局对象A 构造函数里引用上文中饿汉形式的指针，若在A构造函数构造之前以上单例并未构造出来，那就会有问题。例如[A在其类构造函数中引用了B类对象](https://blog.csdn.net/crayondeng/article/details/24853471)

C++对全局对象的构造顺序没有规定，但在很多编译器里边，同一个文件出现的多个全局对象，会按照出现的先后顺序进行初始化。所以，如果A的构造函数中使用initance（）函数引用单例类的成员变量的确会出现问题。不过，在构造函数里使用其它类的成员变量，一直都是危险形为，编程时都要多加小心才行。
