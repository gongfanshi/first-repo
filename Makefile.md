```bash
#Makefile内容通常由以下3部分组成
#<目标名称>:<前置依赖>
#	\t<需要执行的命令>


#定义变量名，随便取，这里叫objects
#\后面不能有空格  
#   :=和=效果一样
objects = sum.o main.o


#1、放在第一个的是默认目标
#	目标是边溢出main文件	依赖sum.o main.o文件
#	编译的命令是g++ sum.o main.o -o main
#2、 main:sum.o main.o
# 		g++ sum.o main.o -o main替换为变量名
#3、前面目标名称为main,后面-o main，最终是由-o main那个名字决定的
#	但是不要改成不一样的，因为前面目标名称main是追踪的目标，-o main是生成的文件名字，如果不一样，重复执行make会重复生成，
#	因为main对不上，就无法显示已是最新
#4、自动推导甚至可以让依赖是.cpp文件，不需要要是.o文件
main:$(objects)
	g++ $(objects) -o main

#1、其实不加这一段也行,-o依赖的会自动推导出来
#	可以推出.cpp文件，但不能推导出sum.h
#	.cpp文件发生变换是可以被监视到的，make会生成新的，但.h文件发生变换不会被监视到，make显示main已经是最新的
#	自动推导是分层次的，我们可以删除能自动推导的，只保留不能的，例如：
#	main.o: sum.h	把能删的都删了
#2、这种加上这一段是好的，根据依赖更新原则，这个依赖更晚就只更新这个，其他的不用更新了，否则一行的话就要全部执行gcc
main.o:main.cpp sum.h
	g++ -c main.cpp -o main.o

sum.o:sum.cpp sum.h
	g++ -c sum.cpp


#声明伪目标
#如果文件夹中有个叫clean的文件，会发生冲突，此时需要声明伪目标
#伪目标不会生成任何文件
.PHONY : clean

#只要main,不需要显示main.o和sum.o，就把他们删除了
#make clean即可
#rm多次重复删除会报已经删除的错误，前面加个'-'会忽略错误
clean:
#	-rm main main.o sum.o
	-rm main $(objects)



# 预定义变量，系统规定好的
# 	-AR:归档维护程序的名称，默认值为ar
# 	-CC : c编译器的名称,默认值为cc(即为gcc)
# 	-CXx :C++编译器的名称,默认值为g++
# 	-$@:目标的完整名，就是前面的目标名称
# 	-$<:第一个依赖文件的名称
# 	-$^:所有的依赖文件
# 获取变量的值： $(变量名)


#可以省去相同的操作，融合成一个类似的
# %.o:%.c
# 	-%：通配符,匹配一个字符串
# 	-两个%匹配的是同一个字符串


#函数
# $ (wildcard * .c ./sub/ *.c)
# 返回值格式:a.c b.c c.c d.c e.c f.c
# $ (patsubst %.c,%.o,x.c bar.c)
# 返回值格式:x.o bar.o

```

