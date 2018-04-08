#include<stdio.h>			
#include<stdlib.h>
#include<conio.h>				//控制台输入输出函数
#include<string.h>
#define MAX 4

typedef struct bookinf			//存放图书信息的链表
{
	char num[10];				//编号
	char name[20];				//书名
	char aut[10];					//作者
	char pub[30];				//出版社	
	float price;					//价格
	int book_kc;					//库存数量
	int book_xy;					//现有数量
	struct bookinf *next;
}BOOK;							//将链表重定义为BOOK


typedef struct borrow			//存放借书信息的链表
{
	char borrow_num[10];		//借书的编号
	char limit_date[8];			//借书的期限
}BORROW;						//将链表重定义为BORROW


typedef struct reader				//存放读者信息的链表
{
	char reader_num[10];		//读者编号
	char reader_name[10];		//读者姓名
	int keep_num;				//借书数量
	BORROW borrow[MAX];		//读者信息链表嵌套借书信息链表
	struct reader *next;
}READER;


BOOK *head_book;				//定义指向图书信息的全局头指针
READER *head_reader;			//定义指向读者信息的全局头指针


void login();						//登录
void ini();						//初始化
void ini_book();					//初始化图书
void ini_reader();					//初始化读者
void Load();						//载入
void Load_Reader();				//载入读者
void Load_Book();				//载入图书
void main_menu();				//主菜单
int admin_menu();				//管理员菜单
int reader_menu();				//读者菜单
void in();						//新书入库
void show();						//显示详情
void search();					//查询信息
void search_book();				//查询图书
void search_reader();				//查询读者
void Borrow();					//借书
void Return();					//还书
void Save() ;						//保存
void Save_Reader();				//保存读者
void Save_Book();				//保存图书 
void Modify();                 //修改图书




void login()
{
	system("cls");
	printf("欢迎使用!\n\t\t\t\t图书管理系统\n");
	printf("\n\t\t\t\t\t\t\t\t作者:高佩雯\n");
	printf("\n\n\n按 任 意 键 进 入 系 统 ...");
	getch();
	system("cls");
}




void ini()
{
	ini_reader();
	ini_book();
}
void ini_reader()	
{ 
  READER *p0;                                //定义读者链表指针p0
    int i;
    printf("\n读者初始化开始,请输入读者信息\n");
    p0=(READER *)malloc(sizeof(READER));		//为p0申请空间	
    head_reader=p0;					       //头指针指向p0
    printf("读者编号:");				          //输入读者编号（唯一）
    scanf("%s",p0->reader_num); 
    printf("读者姓名:");				          //输入读者姓名（不唯一）
    scanf("%s",p0->reader_name);
    p0->keep_num=0;
    for(i=0;i<MAX;i++)
    {
		strcpy(p0->borrow[i].borrow_num,"0");	//所借图书直接置为0(即没有借书)
		strcpy(p0->borrow[i].limit_date,"0"); 
     }
	p0->next=NULL;					//链表中此时只有一个成员
    printf("\n读者信息初始化完毕!按任意键继续\n"); 
	getch();
	system("cls");
}


void ini_book()	
{
	BOOK *p0;
	printf("\n图书初始化开始,请输入图书信息\n");
	p0=(BOOK *)malloc(sizeof(BOOK));
	head_book=p0;					//头指针指向p0
	printf("图书编号:");				//输入图书编号(唯一)
    scanf("%s",p0->num);
	printf("书名:");				//输入图书名称
    scanf("%s",p0->name);
	printf("作者:");				//输入图书作者
    scanf("%s",p0->aut);
	printf("出版社:");				//出版社
	scanf("%s",p0->pub);
	printf("价格:");				//价格
	scanf("%f",&p0->price);
	printf("图书数量:");			        //输入图书数量
    scanf("%d",&p0->book_kc);
	p0->book_xy=p0->book_kc;		        //开始时图书现有量和库存量相等
    p0->next=NULL;					//图书链表此时只有一个成员
	printf("\n图书信息初始化完毕!按任意键继续下一步操作...\n");
	getch();
	system("cls");
}



void Load()
{
	Load_Reader();
	Load_Book();
}
void Load_Reader()					
{
   READER *p1,*p2,*p3;
   FILE *fp;
   fp=fopen("Reader.dat","rb");
   p1=(READER *)malloc(sizeof(READER));
   fread(p1,sizeof(READER),1,fp);
   head_reader=p3=p2=p1;
   while(!feof(fp))
   {
	   p1=(READER *)malloc(sizeof(READER));
	   fread(p1,sizeof(READER),1,fp);
	   p2->next=p1;
	   p3=p2;
	   p2=p1;
   }
   p3->next=NULL;
   free(p1);
   fclose(fp);
}
void Load_Book()					
{
	BOOK *p1,*p2,*p3;
	FILE *fp;
	fp=fopen("Book.dat","rb");
    p1=(BOOK *)malloc(sizeof(BOOK));
	fread(p1,sizeof(BOOK),1,fp); 
    head_book=p3=p2=p1;
	while(!feof(fp))
	{
		p1=(BOOK *)malloc(sizeof(BOOK));
		fread(p1,sizeof(BOOK),1,fp);
		p2->next=p1;
		p3=p2;
		p2=p1;
	}
	p3->next=NULL;
    free(p1);
	fclose(fp);
}




void main_menu()
{
	int n;																			
	printf("\n请选择您的身份:\n");
	printf("\t1.管理员\n\t2.读者\n<退出请按其他任意键>:\n");
	scanf("%d",&n);
	switch(n)
	{
		case 1:
			{
					do{
						switch(admin_menu())
						{
							case 0:
								system("cls");
								Save();
								printf("\n\n\t文件保存成功!\n"); 
								printf("\n\n\t欢迎下次使用本系统!\n"); 
								getch(); 
								exit(0);
							case 1:
								in();		//录入函数
								break;
							case 2:
								search();	//查询函数
								break;
							case 3:
								Borrow();	//借出函数
								break;
							case 4:
								Return();       //还入函数
								break;
							case 5:
								Modify();       //修改函数
								break;
							default:
								Save();
								printf("\n错误!");
								break;
						}										
					}while(1);
					
					
			}
		case 2:
			{
				switch(reader_menu())
					{
						case 0:
							system("cls");
							printf("\n\n\t欢迎下次使用本系统!\n"); 
							getch(); 
							exit(0);
						case 1:
							show();															//瞬间退出
							getch();
							break;
						case 2:
							search();
							break;
						default:
							Save();
							printf("\n错误!");
							break;
					}

			}break;
		default:
			exit(0);
	}
}





int admin_menu()
{
	int n;
	system("cls");
	printf("\n\n\n\n");
	printf("=========================================\n");
	printf("\t图书管理系统\n");
	printf("=========================================\n");
	printf("\t0.退出系统\n");
	printf("\t1.新书入库\n");
	printf("\t2.查询信息\n");
	printf("\t3.借书登记\n");
	printf("\t4.还书管理\n");
	printf("\t5.修改图书\n");
	printf("=========================================\n");
	printf("\n请选择(0-5):");
	for(;;)							//for空语句循环调用,保证能进行重复操作
	{ 
		scanf("%d",&n); 
		if(n<0||n>5)
			printf("\n错误!请重新输入:");
		else break;
	}
	return n;						//将输入值返回给admin_menu
}



int reader_menu()
{
	int n;
	system("cls");
	printf("==================================\n");
	printf("\t图书管理系统\n");
	printf("==================================\n");
	printf("\t0.退出系统\n");
	printf("\t1.显示详情\n");
	printf("\t2.查询信息\n");
	printf("===================================\n");
	printf("\n请选择(0-2):");
	for(;;)											
	{ 
		scanf("%d",&n); 
		if(n<0||n>2)
			printf("\n错误!请重新输入:");
		else break;
	}
	return n;									
}




void in()
{
	BOOK *p,*p0,*p1;					//指针p1用于存放新的录入信息，p0存放库存
	p=p0=head_book;						//暂时为头指针								
	printf("请输入新书信息:\n");
	p1=(BOOK *)malloc(sizeof(BOOK));
	printf("图书编号:");
    scanf("%s",p1->num);
    while(strcmp(p1->num,p0->num)!=0&&p0->next!=NULL)//输入的图书编号和头指针不等且第一个节点的尾指针不指向NULL时，p指向下一个节点
		p0=p0->next;
    if(strcmp(p1->num,p0->num)==0)				//若图书编号存在,则直接进库,只须输入书的数量
    {
		printf("此编号图书已存在!直接入库!\n"); 
        printf("图书数量:");
        scanf("%d",&p1->book_kc);
		p0->book_kc+=p1->book_kc;			//将输入数量计入库存
		p0->book_xy+=p1->book_kc;			//现有量相应增加
    }
    else							//若不存在,则需要输入其他的信息 ,然后在进行插入操作
    {
		printf("书名:");
		scanf("%s",p1->name);
		printf("作者:");
		scanf("%s",p1->aut);
		printf("出版社:");
		scanf("%s",p1->pub);
		printf("价格:");
		scanf("%f",&p1->price);
		printf("数量:");
		scanf("%d",&p1->book_kc);
		while(p->next)					//p的指向后移，直到指向尾节点NULL 
		{
			p=p->next;
		}
		if(head_book==NULL)
		 head_book=p1;					//若链表中没有数据,head直接指向p0处,即指向输入的第一个节点数据
		else p->next=p1;				//若链表中有数据,链表中最后元素的next指向p0处,即指向最后输入的节点数据
		p1->next=NULL;					//p0的尾指针指向NULL,录入结束
		p1->book_xy=p1->book_kc;			//因为录入的是原本不存在的新书，所以现有等于库存 
	}
    printf("\n新书入库完毕!按任意键继续下一步操作...\n");
	getch();
    system("cls");
}




void show()
{
	BOOK *p;
	p=head_book;
	printf("\n所有图书信息如下：\n");
	printf("\n编号\t书名\t作者\t出版社\t价格\t现有量\t库存量\n");
	do
	{
		printf("%s\t%s\t%s\t%s\t%5.2f\t%d\t%d\n",p->num,p->name,p->aut,p->pub,p->price,p->book_xy,p->book_kc);
		p=p->next;
	}while(p!=NULL);
}




void search()																					
{
	int n;
	printf("\n\t图书信息查询菜单\n");
	printf("========================\n");
	printf("\t0.返回\n");
	printf("\t1.查询图书\n");
	printf("\t2.查询读者\n");
	printf("\n");
	printf("请选择相应代码:");
	for(;;)
	{ 
		scanf("%d",&n); 
		if(n<0||n>4)
			printf("\n错误!请重新输入:");
		else 
			break;
	}
	switch(n)
	{
		case 1:
			search_book();
			break;
		case 2:
			search_reader();
			break;
		default:
			break;
	}
}




void search_book()
{
	BOOK *p;						//结构体指针p即指向图书
	char search_book[10];					//读者编号唯一,查找只须一次遍历
	p=head_book;
	printf("请输入你要查询图书编号:\n");
	scanf("%s",search_book);
	while(strcmp(p->num,search_book)!=0&&p->next)
		p=p->next;
		if(strcmp(p->num,search_book)==0)		//找到了就打印,否则提示不存在,或出错  
		{
			printf("\n编号为%s的图书信息如下:\n",search_book);
			printf("\n图书编号\t书名\t作者\t出版社\t价格\t现有\t库存\n");
			printf("%s\t%s\t%s\t%s\t%f\t%d\t%d\n",p->num,p->name,p->aut,p->pub,p->price,p->book_xy,p->book_kc);
		}
		else
			printf("\n此图书编号不存在,请核对!按任意键返回...");
			getch();
			system("cls");
}




void search_reader()
{
	READER *p;						//结构体指针p即指向读者
	int i;
	char search_reader[10];					//存放读者编号
	p=head_reader;
	printf("\n请输入你要查询读者编号:");
	scanf("%s",search_reader);
	while(strcmp(p->reader_num,search_reader)!=0&&p->next)	//对读者链表进行遍历，查找是否有输入的编号
		p=p->next;
	if(strcmp(p->reader_num,search_reader)==0)
	{
		printf("\n编号为%s的读者信息为:\n",search_reader);
		printf("读者编号\t读者姓名");			//此处这样处理,是为了能实现一个读者能借多本书 MAX 可以改
		printf("\n");
		printf("\t%s\t\t%s",p->reader_num,p->reader_name);
		for(i=0;i<MAX;i++)
		{
			printf("\n");
			printf("借的第%d本",i+1); 
			printf("\t还第%d本书的时间",i+1); 
			printf("\n");
			printf("\t%s",p->borrow[i].borrow_num);
			printf("\t\t%s",p->borrow[i].limit_date); 
		}
		printf("\n");
	}
	else
		printf("\n此读者编号不存在,请核对!按任意键返回...");
		getch(); 
		system("cls");
}




void Borrow()
{
	BOOK *p0; READER *p1;
	char bo_num[10],rea_num[10],lim_date[8];
	int i;
	p0=head_book; p1=head_reader;
	printf("请输入借书的读者编号:\n");					//先输入借书的读者信息
	scanf("%s",rea_num);
	while(p1->next!=NULL&&strcmp(rea_num,p1->reader_num)!=0)		//遍历一次读者链表,判断读者是否存在
		p1=p1->next;
	if(p1->next==NULL&&strcmp(rea_num,p1->reader_num)!=0)			//如果存在,则继续,不存在则提示错误!
    { 
		printf("\n此读者编号不存在!按任意键返回..\n");
		getch();
		system("cls");
    } 
	printf("请输入要借的书的编号:\n"); 
	scanf("%s",bo_num);
	while(strcmp(bo_num,p0->num)!=0&&p0->next!=NULL)			//遍历一次图书链表,判断此图书是否存在
		p0=p0->next;
    if(p0->next==NULL&&strcmp(bo_num,p0->num)!=0)				//如果存在,则继续,不存在则提示错误!
    {
		printf("此图书编号不存在!按任意键返回...\n");
     }
    else if(p0->book_xy<=0)							//判断此图书现在是否还有库存  
    {
		printf("抱歉,此书已借完!请等待新书的到来!\n按任意键返回...");
    } 
	else if(p1->keep_num>=MAX)						//判断此读者借书权限是否已达最大
    {
		printf("\n不好意思,借书数目已满!不能借书!\n按任意键返回...");
    }
    else if(strcmp(p1->borrow[0].borrow_num,"0")!=0)				//此读者若已经借书,再判断此读者是否要借两本一样的书
     {
		for(i=0;i<MAX;i++)						//遍历一次读者所借的书,如果发现有重复,则结束
		{
			if(strcmp(p1->borrow[i].borrow_num,bo_num)==0)
			{
				printf("抱歉!同一个读者不能同借两本相同的书!\n");
			}
			else if(strcmp(p1->borrow[i].borrow_num,"0")==0)	//如果没有重复
			{
				printf("\n请输入你要归还图书的日期:"); 
				scanf("%s",lim_date);
				strcpy(p1->borrow[p1->keep_num++].borrow_num,bo_num);//记录所借书号,并且借书数目加一,现存量减一
				strcpy(p1->borrow[p1->keep_num-1].limit_date,lim_date);   
				p0->book_xy--;
				printf("\n编号为%s的读者借书完毕!按任意键继续下步操作...",p1->reader_num);
           }
        }
     }
     else							                //此读者从未借过书 
     {
		printf("请输入你要归还图书的日期:"); 
		scanf("%s",lim_date);
		strcpy(p1->borrow[p1->keep_num++].borrow_num,bo_num);		//记录所借书号,并且借书数目加一,现存量减一
		strcpy(p1->borrow[p1->keep_num-1].limit_date,lim_date); 
		p0->book_xy--;
		printf("\n读者编号%s借书完毕!按任意键继续下步操作...",p1->reader_num);
     }
    getch();
	system("cls");
}




void Return()
{
	BOOK *p; READER *q;
	int i,j,find=0;
	char return_book_num[10],return_reader_num[10];
	p=head_book; q=head_reader;
	printf("请输入要还书的读者编号:");
	scanf("%s",return_reader_num);
	while(q->next!=NULL&&strcmp(return_reader_num,q->reader_num)!=0) 
		q=q->next;
	if(q->next==NULL&&strcmp(return_reader_num,q->reader_num)!=0)
	{
		find=2;	                                                                //设置标志,用于判断读者是否借了此书或是否出错提前结束,若出错,则find=2,若借了,则find=1,若没借为find=0
		printf("\n此读者编号不存在!按任意键返回...\n");
		getch(); 
		system("cls"); 
	}
	printf("\n请输入读者所还的图书编号:");
    scanf("%s",return_book_num);
	while(p->next!=NULL&&strcmp(return_book_num,p->num)!=0)
         p=p->next;
	if(p->next==NULL&&strcmp(return_book_num,p->num)!=0)
    {
		find=2;		
		printf("\n错误!此图书编号不存在!按任意键返回...\n");
		getch(); 
		system("cls");
    } 
	for(i=0;i<MAX;i++)
		if(strcmp(return_book_num,q->borrow[i].borrow_num)==0)	                //如果此读者借了此书
		{
			find=1;			
			for(j=i;j<MAX-1;j++)
			{
				strcpy(q->borrow[j].borrow_num,q->borrow[j+1].borrow_num); 
				strcpy(q->borrow[j].limit_date,q->borrow[j+1].limit_date); 
			}
			strcpy(q->borrow[MAX-1].borrow_num,"0");
			strcpy(q->borrow[MAX-1].limit_date,"0");
			p->book_xy++;
			q->keep_num--;
			printf("\n编号为%s的读者还书完毕!按任意键继续下步操作...",return_reader_num);
       }
		if(find==0)                                                             //为什么有个find==0,因为遍历时循环MAX次,如循环完毕还没有找到,则说明读者没有借此书,find用来保证只打印一次结果,否则每循环一次就会打印一个结果
			printf("\n错误!此读者未借此书!按任意键返回..\n");		//如果遍历读者所借的书号,发现读者未借此书,则提示错误!
		getch(); 
		system("cls");
}




void Save()
{
	Save_Reader();
	Save_Book();
}




void Save_Reader()	
{
	FILE *fp_reader;						                //创建文件型指针
	READER *p,*p0;
	p=head_reader;
	if((fp_reader=fopen("Reader.dat","wb+"))==NULL)					//创建文件,进行保存
	{
		printf("\n文件保存失败!\n请重新启动本系统...\n");
		exit(0);
	}
	while(p!=NULL) 
	{
		if(fwrite(p,sizeof(READER),1,fp_reader)!=1)			         //将链表中的信息写入文件中
		printf("\n写入文件失败!\n请重新启动本系统!\n");
		p0=p;
		p=p->next;
		free(p0);						                 //释放所有结点
	}
	head_reader=NULL;
	fclose(fp_reader);						                 //关闭文件
}





void Save_Book()	
{
	FILE *fp_book;
	BOOK *p,*p0;
	p=head_book;
	if((fp_book=fopen("Book.dat","wb+"))==NULL)
	{
		printf("\n文件保存失败!\n请重新启动本系统...\n");
		exit(0);
	}
	while(p!=NULL) 
	{
		if(fwrite(p,sizeof(BOOK),1,fp_book)!=1)
			printf("\n写入文件失败!\n请重新启动本系统!\n");
			p0=p;
			p=p->next;
			free(p0); 
	}
	head_book=NULL;
	fclose(fp_book);
}









void Modify()
{
  BOOK *p0;                                                       
  printf("\n图书修改开始，请输入图书信息\n");
  p0=(BOOK *)malloc(sizeof(BOOK));
  head_book=p0;                                               //头指针指向p0
  printf("图书编号：");                                       //输入图书编号
  scanf("%s",p0->num);
  printf("书名：");                                           //输入图书名称
  scanf("%s",p0->name);
  printf("作者：");                                           //输入图书作者
  scanf("%s",p0->aut);
  printf("出版社：");                                         //输入出版社
  scanf("%s",p0->pub);
  printf("价格：");                                           //输入价格
  scanf("%f",&p0->price);
  printf("数量：");                                           //输入图书数量
  scanf("%d",&p0->book_kc);
  p0->book_xy=p0->book_kc;                                    //开始时图书现有量和库存量相等
  p0->next=NULL;                                              //图书链表
  printf("\n图书信息修改完成！按任意键继续下一步操作...\n");
  getchar();
  system("cls");
}




void main() 
{
    FILE *fp_book,*fp_reader;
    login(); 
    if((fp_book=fopen("Book.dat","rb"))==NULL||(fp_reader=fopen("Reader.dat","rb"))==NULL)
     {
		ini();
     }
    else
		Load();
		main_menu();
}
