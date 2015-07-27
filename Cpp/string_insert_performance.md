有这样一个网络传输包，前端有个固定的包头，包含了后面传输body的长度信息，具体示意图如下

|<-    header           ->|<-    body      ->|   
|version  | proto | length|     content      |

比如我们有一个protobuf的结构体，需要序列化成为string，并将其作为body发送出去。

在有拷贝的前提下，我们选用什么性能比较高呢？

 1. 方案一
        复用data_buffer string 将 Header 头 insert到data_buffer中，将大量的字符串后移定长。
 2. 方案二
        将Header外化一个string，然后调用append函数，将data_buffer的字符拷贝到head的string中去。
 3. 方案三
        分配内存，memcpy 过去。
 4. 方案四
        不分配内存，利用栈空间（受限），memcpy过去。
        
 于是我做了如下的实验，示例代码如下（用-02编译）
 ```cpp
#include <string>
#include <stdio.h>
#include "Utility.h"
int load_file(const char* filename, char** content, size_t* content_len)
{
    FILE* fp = fopen(filename, "r");
    if (!fp)
    {
        return -1;
    }
    fseek(fp, 0, SEEK_END);
    size_t len = ftell(fp);
    rewind(fp);
    char* buf = (char*)malloc(len + 1);
    if (!buf)
    {
        return -2;
    }
    fread(buf, sizeof(char), len, fp);
    buf[len] = '\0';
    fclose(fp);
    *content = buf;
    *content_len = len;
    return 0;
}

int main(int argc, const char *argv[])
{
    char* content;
    const char* file_name = argv[1];
    uint32_t space = atoi(argv[2]);
    uint32_t insert = atoi(argv[3]);
    size_t len = 0;
    if (load_file(file_name, &content, &len) < 0)
    {
        printf("load %s failed\n", file_name);
        return -1;
    }
    std::string raw_content(content, len);
    for (int i = 0; i < space - 1 ; i++)
    {
        raw_content.append(content, len);
    }
    char size_str[64];
    snprintf(size_str, sizeof(size_str), "%u\t%u",
             insert, raw_content.size());
    std::string final_content("cooooooo%dddd$%DDD123r423");
    {
      TimeEval timer(size_str);
      if(insert == 0 )
      {
         raw_content.insert(0, final_content);
      } if (insert == 1)
      {
         final_content.append(raw_content);
      }
      if (insert == 2)
      {
          char* buf = (char*)malloc(final_content.size() + raw_content.size() + 1);
          memcpy(buf, final_content.c_str(), final_content.size());
          memcpy(buf + final_content.size(), raw_content.c_str(), raw_content.size());
          free(buf);
      }
    }
    return 0;
}
 ```
 