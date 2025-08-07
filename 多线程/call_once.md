# std::call_once

call_once 正如其命名一样，可以使得某个函数或者方法正好只调用一次，无论有多少个线程尝试调用call_once()(在同一个once_flag)都同样如此。这个函数还常常用来作为单例模式的创建。某个特定的 once_flag 实例的有效调用在对同一个 once_flag 实例的其他所有 call_once() 调用之前完成。在同一个 once_flag 实例上调用 call_once()的其他线程都会阻塞，直到有效调用结束。

```cpp
std::once_flag g_onceFlag;

void initializeSharedResources()
{
	// ... Initialize shared resources to be used by multiple threads.
	std::cout << "Shared resources initialized.\n" ;
}


void processingFunction()
{
	// Make sure the shared resources are initialized.
	std::call_once(g_onceFlag, initializeSharedResources);

	// ... Do some work,Including using the shared resources
	std::cout << "Processing\n";
}

int main()
{
	std::vector<std::thread> threads{ 3 };
	for (auto& t: threads)
	{
		t = std::thread{ processingFunction };
	}
	for (auto& t : threads) t.join();
}
```

output：

```txt
Shared resources initialized.
Processing
Processing
Processing

···