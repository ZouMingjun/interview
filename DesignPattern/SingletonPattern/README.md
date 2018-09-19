# 单例模式

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
		return pInstance;
	}
};

// 线程安全的单例模式
//1、属于内部静态变量的懒汉经典模式
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
```
//2、加锁的懒汉模式
class singleton
{
protected:
    singleton()
    {
        pthread_mutex_init(&mutex);
    }
private:
    static singleton* p;
public:
    static pthread_mutex_t mutex;
    static singleton* initance();
};

pthread_mutex_t singleton::mutex;
singleton* singleton::p = NULL;
singleton* singleton::initance()
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
