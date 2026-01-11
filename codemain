#include<stdio.h>
#include<string.h>
#include<stdlib.h>

typedef struct Book {
    char isbn[20];          
    char title[50];         
    char author[30];        
    char category[20];      // 分类号
    char publisher[50];     
    char pub_date[20];      // 格式: YYYY-MM-DD
    float price;            
    int status;             // 1: 在馆, 0: 已借出
    char borrower_id[20];   
    struct Book* next;      
} Book;

typedef struct Reader {
    char id[20];            
    char class_name[20];    
    char name[20];          
    char gender[10];       
    struct Reader* next;    
} Reader;

Book* bookHead = NULL;
Reader* readerHead = NULL;
const char * BOOK_FILE = "books.txt";
const char * READER_FILE = "readers.txt";

void menu(void);
void logo(void);
void pause(void);
void pausesave(void);

void loadData(void);
void saveData(void);

Book* findBookByISBN(char isbn[]);
Reader* findReaderByID(char id[]);

void addBook(void);
void editBook(void);
void browseBooks(void);				//排序
void searchBook(void);

void registerReader(void);
void borrowBook(void);
void returnBook(void);


int main(void){
	loadData();					//打开文件
	logo();
	int choice;
	while (1) {
		menu();		
		scanf("%d",&choice);
		switch(choice){
            case 1: addBook(); break;
            case 2: editBook(); break;
            case 3: browseBooks(); break;
            case 4: searchBook(); break;
            case 5: registerReader(); break;
            case 6: borrowBook(); break;
            case 7: returnBook(); break;
            case 8: saveData(); printf("数据已保存，退出系统。\n");pausesave();
            default: printf("输入无效，请重试。\n"); pause();
		}
	}
return 0;
}

void pause(void) {
    printf("\n按回车键继续...");
    while (getchar() != '\n');            // 清除输入缓冲区中的所有残留字符
    getchar();
}

void pausesave(void){
	printf("\n感谢使用，欢迎再来...");
	while(getchar() != '\n');
}

void logo(void){
    const char *logo[] = {
        "              ...........              ",
        "         .....***********.....         ",
        "       ..****.............****..       ",
        "      .***...................***.      ",
        "     .***.....................***.     ",
        "    .***.......................***.    ",
        "   .***..........*************..***.   ",
        "  .***..........***************..***.  ",
        "  ***..........***................*** ", 
        "  ***.........***.................*** ", 
        " ***.........*************........*** ", 
        " ***.........************.........*** ", 
        "  ***........***..................*** ", 
        "  ***.......***...................*** ", 
        "  ***..****************...........*** ", 
        "  .***..**************...........***.  ",
        "   .***.........................***.   ",
        "    .***.......................***.    ",
        "     ..***...................***..     ",
        "       ..****.............****..       ",
        "         .....***********.....         ",
        "              ...........              "
    };

    const char *hcr[] = {
     "",
        "",
        "",
        "   ###      ######    ######    #    #  ",
        "   #  #     #    #    #    #    #   #  ",
        "   #    #   #    #    #    #    #  #  ",
        "   #  #     #    #    #    #    # #   ",
        "   ##       #    #    #    #    ##    ",
        "    #  #     #    #    #    #    # #   ",
        "    #    #   #    #    #    #    #  #  ",
        "    #  #     #    #    #    #    #   #  ",
        "    ###      ######    ######    #    # ",
        "                                    ",
        "                                    ",
        "          图 书 管 理 系 统         ",
        "                                    ",
        "         按下回车键继续........        ",
        "                                    ",
        "                                    ",
        "                                    ",
        "                                    ",
    };

    int rows = sizeof(logo) / sizeof(logo[0]);

    printf("\n");
    for (int i = 0; i < rows; i++) {
        // 校徽
        printf("%s", logo[i]);
        
        // 间距
        printf("      "); 
        
        // 文字
        printf("%s", hcr[i]);
        
        printf("\n");
    }
    printf("\n");
    while (getchar() != '\n'); 
    getchar(); 
}

void menu(void) {
    system("cls"); // Windows清屏
    printf("==========================================\n");
    printf("           图书管理系统 \n");
    printf("==========================================\n");
    printf("  1. 图书信息录入\n");
    printf("  2. 图书信息编辑 (修改/删除)\n");
    printf("  3. 图书信息浏览 (排序查看)\n");
    printf("  4. 图书信息查询 (模糊查找)\n");
    printf("  5. 读者注册\n");
    printf("  6. 办理借书\n");
    printf("  7. 办理还书\n");
    printf("  8. 保存并退出\n");
    printf("==========================================\n");
    printf("请输入选项: ");
}

void saveData(void) {
	getchar();
    FILE* fp = fopen(BOOK_FILE, "w");
    if (fp == NULL){
		printf("书本文件存储失败！");
	}
    else{
        Book* p = bookHead;
        while (p != NULL) {
        	fprintf(fp, "%s %s %s %s %s %s %.2f %d %s\n",
                p->isbn, p->title, p->author, p->category, p->publisher, 
                p->pub_date, p->price, p->status, 
                strlen(p->borrower_id) > 0 ? p->borrower_id : "NULL");
            p = p->next;
        }
        fclose(fp);
    }
    printf("图书数据已保存到 %s\n", BOOK_FILE);
    
    
    fp = fopen(READER_FILE, "w");    
    if (fp == NULL){
		printf("读者文件存储失败！");
	}
    else{
        Reader* r = readerHead;
        while (r != NULL) {
            fprintf(fp, "%s %s %s %s\n", r->id, r->class_name, r->name, r->gender);
            r = r->next;
        }
        fclose(fp);
    }
    printf("读者数据已保存到 %s\n", READER_FILE);
}

void loadData(void) {
    // 读取图书
    FILE* fp = fopen(BOOK_FILE, "r");
    if (fp == NULL){
		printf("书本文件读取失败！");
	}
    else{
        while (!feof(fp)) {
            Book* node = (Book*)malloc(sizeof(Book));
            char temp_borrower[20];
            int flag = fscanf(fp, "%s %s %s %s %s %s %f %d %s", 
                node->isbn, node->title, node->author, node->category, 
                node->publisher, node->pub_date, &(node->price), &(node->status), temp_borrower);
            if (flag == 9) {
                if (strcmp(temp_borrower, "NULL") == 0) strcpy(node->borrower_id, "");
                else strcpy(node->borrower_id, temp_borrower);
                node->next = bookHead;
                bookHead = node;
            } else {
                free(node);
                break;
            }
        }
        fclose(fp);
    }

    // 读取读者
    fp = fopen(READER_FILE, "r");
    if (fp == NULL){
		printf("读者文件读取失败！");
	}
    if (fp) {
        while (!feof(fp)) {
            Reader* node = (Reader*)malloc(sizeof(Reader));
            if (fscanf(fp, "%s %s %s %s", node->id, node->class_name, node->name, node->gender) == 4) {
                node->next = readerHead;
                readerHead = node;
            } else {
                free(node);
                break;
            }
        }
        fclose(fp);
    }
}

Book * findBookByISBN(char isbn[]){
	Book *p = bookHead;
	while(p != NULL){
		if(strcmp(p->isbn,isbn) == 0){
			return p;
		}
		p = p->next;
	}
	return NULL;
}

Reader * findReaderByID(char id[]){
	Reader * p= readerHead;
	while (p != NULL){
		if(strcmp(p->id,id) == 0){
			return p;
		}
		p = p->next;
	}
	return NULL;
}

void addBook(void){
	Book * node = (Book *)malloc(sizeof(Book));
	printf("请输入书号（IBSN码）"); scanf("%s",node->isbn);
	
	if (findBookByISBN(node->isbn) != NULL){
		printf("提醒：这本书号已经存在！\n");
		free(node);
		pause();
		return;
	}
	else{
    printf("请输入书名: "); scanf("%s", node->title);
    printf("请输入作者: "); scanf("%s", node->author);
    printf("请输入分类号: "); scanf("%s", node->category);
    printf("请输入出版社: "); scanf("%s", node->publisher);
    printf("请输入出版时间(YYYY-MM-DD): "); scanf("%s", node->pub_date);
    printf("请输入价格: "); scanf("%f", &(node->price));
    
    node->status = 1;								//默认书籍在馆内
    strcpy(node->borrower_id  ,  "");
	
	node->next = bookHead;
	bookHead = node;
	printf("录入成功！ \n");
	pause();
	}
}

void registerReader(void){
	Reader* node = (Reader *)malloc(sizeof(Reader));
	printf("请输入学号：");
	scanf("%s",node->id);
	
	if(findReaderByID(node->id) != NULL){
		printf("该学号已注册！\n");
		free(node);
		pause();
		return;
	}
	else{
	printf("请输入姓名: "); scanf("%s", node->name);
    printf("请输入班级: "); scanf("%s", node->class_name);
    printf("请输入性别: "); scanf("%s", node->gender);
    
    node->next = readerHead;
    readerHead = node;
    printf("读者注册成功！\n");
    pause();
	}
}

void editBook(void){
    Book* p = bookHead;
    Book* prev = NULL;							//前一本书
    
	printf("1. 修改图书  2. 删除图书\n请选择: ");
	int type;
	scanf("%d",&type);

	char isbn[20];
	printf("请输入要操作的书号: ");
	scanf("%s", isbn);
	
	while(p != NULL){
		
		if(strcmp(p->isbn,isbn) == 0){
			break;
		}
		else{
			prev = p;
			p = p->next;
		}
	
	}
	
	if(p == NULL){
		printf("没有找到该图书。\n");
		pause();
		return ;
	}
	
	if(type == 1){
        printf("当前书名: %s，输入新书名: ", p->title); scanf("%s", p->title);
        printf("当前价格: %.2f，输入新价格: ", p->price); scanf("%f", &(p->price));
        printf("当前状态: %s，输入状态(1在馆/0借出): ", p->status==1?"在馆":"借出"); scanf("%d", &(p->status));
        printf("修改成功！\n");
	}
	else if(type == 2){
		if(p->status == 0){
			printf("该书已经出借，无法删除！\n");
		}
		else{
			if(prev == NULL){
				bookHead = p->next;						//删除的是第一本书
			}
			else{
				prev->next = p->next;					//让上一本数的next指向被删除书的next
			}
			free(p);
			printf("删除成功！\n");
		}
	}
	pause();
}

void borrowBook(void) {
    char id[20], isbn[20];
    printf("请输入借书人学号: ");
	scanf("%s", id);
	
    if (!findReaderByID(id)) {
        printf("未找到该读者，请先注册。\n");
        pause();
        return;
    }
    
    printf("请输入图书书号: "); scanf("%s", isbn);
    Book* p = findBookByISBN(isbn);
    
    if (p == NULL) {
        printf("图书不存在。\n");
    }
	else if (p->status == 0) {
        printf("该书已被借出！\n");
    }
	else {
        p->status = 0;
        strcpy(p->borrower_id, id);
        printf("借阅成功！\n");
    }
    pause();
}

void returnBook(void) {
    char isbn[20];
    printf("请输入归还图书书号: ");
	scanf("%s", isbn);
    Book* p = findBookByISBN(isbn);
    
    if (p == NULL) {
        printf("图书不存在。\n");
    }
	else if (p->status == 1) {
        printf("该书已经在馆，无需归还。\n");
    }
	else {
        p->status = 1;
        strcpy(p->borrower_id, "");
        printf("归还成功！\n");
    }
    pause();
}

void searchBook(void) {
    printf("请输入查询关键字 (书号/书名/作者/出版社): ");
    char key[50];
	scanf("%s", key);
    
    printf("\n%-10s %-20s %-15s %-10s %-10s\n", "书号", "书名", "作者", "出版社", "状态");	//左对齐
    printf("--------------------------------------------------------------\n");
    
    Book* p = bookHead;
    int found = 0;
    while (p != NULL) {						// strstr 实现模糊查询
        if (strstr(p->isbn, key) || strstr(p->title, key) || 
            strstr(p->author, key) || strstr(p->publisher, key)) {
            printf("%-10s %-20s %-15s %-10s %-10s\n", 
                p->isbn, p->title, p->author, p->publisher, 
                p->status == 1 ? "在馆" : "已借出");
            found = 1;
        }
        p = p->next;
    }
    if (found == 0){
	printf("未找到匹配的图书。\n");
	}
    pause();
}

void browseBooks() {
    if (bookHead == NULL) {
        printf("暂无图书信息。\n");
        pause();
        return;
    }

    printf("排序方式: 1.书号 2.书名 3.出版时间 4.价格\n请选择: ");
    int type; 
    if(scanf("%d", &type) != 1) { // 防止输入非数字导致死循环
        while(getchar() != '\n'); // 清空缓冲区
        type = 1; // 默认按书号
    }

    for (Book* i = bookHead; i != NULL; i = i->next) {					// 冒泡排序 (交换节点内容)
        for (Book* j = i->next; j != NULL; j = j->next) {
            int condition = 0;
            switch(type) {
                case 1: condition = strcmp(i->isbn, j->isbn) > 0; break;
                case 2: condition = strcmp(i->title, j->title) > 0; break;
                case 3: condition = strcmp(i->pub_date, j->pub_date) > 0; break;
                case 4: condition = i->price > j->price; break;
                default: condition = 0; break;
            }
            
            if (condition) {

                // 1. 先备份不需要交换的“链表指针”
                Book* i_next_ptr = i->next; 
                Book* j_next_ptr = j->next;
                
                // 2. 备份 i 的全部数据
                Book temp = *i; 

                // 3. 将 j 的数据覆盖到 i
                *i = *j; 

                // 4. 将 i 的原数据（在 temp 中）覆盖到 j
                *j = temp;

                // 5. 还原链表指针（因为 *i=*j 会把指针也覆盖掉，必须还原，否则链表会断）
                i->next = i_next_ptr;
                j->next = j_next_ptr;
                
            }
        }
    }

    // 打印表头
    printf("\n%-20s %-20s %-15s %-10s %-8s %-10s\n", "书号", "书名", "作者", "出版社", "价格", "状态");
    printf("-----------------------------------------------------------------------------------\n");
    Book* p = bookHead;
    while (p != NULL) {
        printf("%-20s %-20s %-15s %-10s %-8.2f %-10s\n", 
            p->isbn, p->title, p->author, p->publisher, p->price, 
            p->status == 1 ? "在馆" : "已借出");
        p = p->next;
    }
    pause();
}
