```c
fputc(ch, fp);//把ch字符写到fp文件中，正常返回该字符的ASCLL码值，错误返回-1
ch = fgetc(fp);//把fp中的一个字符读给ch，读到最后返回-1
//fread和fwrite可以一次读写一个若干长度的数据块
fread(内存地址，字节数，次数，文件指针);//每次读多少字节，读多少次，返回实际读的次数，失败为0
fwrite(内存地址，字节数，次数，文件指针);//每次写多少字节，写多少次，返回实际写入的次数，失败为0
fprintf(fp, "%s\t%d", name, age);//按照%s\t%d的格式向fp中写入name和age，成功返回字节数，否则返回负数
fscanf(fp, "%s\t%d\n", name, &age);//按照%s\t%d的格式从fp中读出字符写到name和age，返回正确则为读入的个数，本样例为2，否则返回-1
fgets(char* str, int n, FILE* stream);//读入正常返回指针，否则为NULL,从stream中一次读入一行
fputs(char* str, FILE* stream);//将str存到stream中，错误返回-1，正确返回非负整数
rewind(fp);//将fp的文件指针重新指向头

//fseek
fseek(fp, 100L, SEEK_SET);//将指针从文件头正向移动100字节
fseek(fp, 100L, SEEK_CUR);//将指针从当前位置正向移动100字节
fseek(fp, -100L, SEEK_END);//将指针从结束位置负向移动100字节

//ftell 用法long int ftell(文件指针)返回当前位置到文件首的偏移字节数
offset = ftell(fp);
```

