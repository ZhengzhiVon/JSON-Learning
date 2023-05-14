# JSON 解析和文件操作

## JSON文件操作
```c
int main()
{
	const char* char_json="{\"name\":\"tulun\"}";
    cJSON* json=cJSON_Parse(char_json);
    char* buf = nullptr;
    printf("data:\n%s\n",buf=cJSON_Print(json));//cJSON_Print()将 cJSON 对象转换为 JSON 格式的字符串。
    
    //文件操作
    //FILE* fp = fopen("tulun.json","w");	非c11写法
    FILE* fp=nullptr;
    errno_t x = fopen_s(&fp,"tulun.json","w");
    if(nullptr==fp)	exit(EXIT_FAILURE);
    fwrite(buf,strlen(cuf),sizeof(char),fp);//文件可改为skoct
    fclose(fp);
    fp = nullptr;
    free(buf);//cJSON_Print() 函数会为返回的字符串分配内存，因此在使用完毕后需要手动释放内存，以避免内存泄漏。可以使用标准库函数 free() 来释放内存
    buf = nullptr;
    
    cJSON_Delete(json);
    json = nullptr;
    return 0;
}

//失效指针，free后需要把指针置空
//野指针：未初始化定义的指针
//cjson的释放：不能直接free，类链表形式,需要用cJSON_Delete
// errno_t x = fopen_s(&fp,"tulun.json","w");
```
}

这段代码使用了 cJSON 库，它是一个轻量级的 JSON 解析器和生成器。代码的主要功能是将一个 JSON 字符串解析成 cJSON 对象，然后将其打印到控制台以及写入到文件中。

具体来说，代码首先定义了一个 JSON 字符串 const char* char_json="{\"name\":\"tulun\"}"，然后使用 cJSON_Parse 函数将其解析成 cJSON 对象。接着，使用 cJSON_Print 函数将 cJSON 对象转换为字符串，并将其打印到控制台中。

之后，代码打开一个名为 tulun.json 的文件，并将 cJSON 对象的字符串表示写入到文件中。在这里，代码使用了 fopen_s 函数打开文件，这是一个 c11 标准中的安全写法，可以避免一些常见的文件操作错误。如果文件打开失败，代码将退出并返回 EXIT_FAILURE。

最后，代码释放了申请的内存，包括 cJSON 对象和字符串缓冲区，并返回 0 表示正常运行结束。

## cJSON_Print()函数
cJSON_Print() 函数是 cJSON 库中的一个函数，用于将 cJSON 对象转换为 JSON 格式的字符串。它的函数原型如下：

```c
char* cJSON_Print(const cJSON *item);
```

其中，item 表示要转换的 cJSON 对象。函数返回一个指向字符串的指针，该字符串包含了 cJSON 对象的 JSON 格式表示。

需要注意的是，cJSON_Print() 函数会为返回的字符串分配内存，因此在使用完毕后需要手动释放内存，以避免内存泄漏。可以使用标准库函数 free() 来释放内存，例如：

```c
char* str = cJSON_Print(json);
printf("JSON string: %s\n", str);
free(str);
```

此外，cJSON_Print() 函数还有一个变体 cJSON_PrintUnformatted()，它可以将 cJSON 对象转换为未格式化的 JSON 字符串，即不包含缩进和换行符。该函数的函数原型如下：

```c
char* cJSON_PrintUnformatted(const cJSON *item);
```

和 cJSON_Print() 函数一样，cJSON_PrintUnformatted() 函数也会为返回的字符串分配内存，同样需要手动释放内存。

假设有以下的 JSON 字符串：

```json
{
    "name": "John Doe",
    "age": 30,
    "city": "New York"
}
```

我们想要在 C++ 代码中解析这个 JSON 字符串并输出它的值。我们可以使用 cJSON 库来完成这个任务。下面是一个使用 cJSON 库的 C++ 代码示例：

```c++
#include <stdio.h>
#include <stdlib.h>
#include "cJSON.h"

int main() {
    const char* json_string = "{\"name\":\"John Doe\",\"age\":30,\"city\":\"New York\"}";
    cJSON* json = cJSON_Parse(json_string);
    if (json != NULL) {
        cJSON* name = cJSON_GetObjectItemCaseSensitive(json, "name");
        cJSON* age = cJSON_GetObjectItemCaseSensitive(json, "age");
        cJSON* city = cJSON_GetObjectItemCaseSensitive(json, "city");
        printf("Name: %s\n", name->valuestring);
        printf("Age: %d\n", age->valueint);
        printf("City: %s\n", city->valuestring);
        cJSON_Delete(json);
    }
    return 0;
}
```

在这个示例中，我们首先定义了一个 JSON 字符串，并使用 cJSON_Parse() 函数将其解析为 cJSON 对象。然后，我们使用 cJSON_GetObjectItemCaseSensitive() 函数获取 JSON 对象中的各个属性，并输出它们的值。

最后，我们使用 cJSON_Delete() 函数释放 cJSON 对象的内存。

## cJSON_GetObjectItemCaseSensitive函数
cJSON_GetObjectItemCaseSensitive() 函数是 cJSON 库中用于获取 JSON 对象中指定名称的成员的函数。它的作用是在 JSON 对象中查找指定名称的成员，并返回该成员的 cJSON 对象指针。

它的函数原型如下：

```c
cJSON *cJSON_GetObjectItemCaseSensitive(const cJSON *const object, const char *const string)
```

其中，第一个参数是要查找的 JSON 对象，第二个参数是要查找的成员名称。这个函数是区分大小写的，如果要忽略大小写，可以使用 cJSON_GetObjectItem() 函数。

如果在 JSON 对象中找到指定名称的成员，则返回该成员的 cJSON 对象指针；如果没有找到，则返回 NULL。

下面是一个示例代码，演示了如何使用 cJSON_GetObjectItemCaseSensitive() 函数获取 JSON 对象中指定名称的成员：

```c
#include <stdio.h>
#include "cJSON.h"

int main() {
    const char *json_str = "{\"name\":\"John\",\"age\":30,\"city\":\"New York\"}";
    cJSON *root = cJSON_Parse(json_str);
    if (root == NULL) {
        printf("Error: Failed to parse JSON string.\n");
        return 1;
    }

    cJSON *name = cJSON_GetObjectItemCaseSensitive(root, "name");
    if (name != NULL) {
        printf("Name: %s\n", name->valuestring);
    } else {
        printf("Error: Failed to get name.\n");
    }

    cJSON_Delete(root);
    return 0;
}
```

在这个例子中，我们解析了一个 JSON 字符串，并使用 cJSON_GetObjectItemCaseSensitive() 函数获取了其中的 "name" 成员。如果成功获取到了该成员，则输出其值；否则输出错误信息。