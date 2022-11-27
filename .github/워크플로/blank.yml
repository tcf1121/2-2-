#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <windows.h>
#define MAP_X_MAX 45
#define MAP_Y_MAX 39
#define MAX_QUEUE_SIZE 10
#define M_S_C 3

int heart = 3;
int score = 0;
//0은 빈공간, 1은 부술 수 있는 블록, 2는 아이템, 3은 부술 수 없는 블록
int blockstate[13][15] = { {0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{3,1,3,1,3,1,3,1,3,1,3,1,3,1,3},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0} };
int blockstate2[13][15] = { {0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
													{3,1,3,1,3,1,3,1,3,1,3,1,3,1,3},
													{0,0,0,0,0,0,0,0,0,0,0,0,0,0,0} };
int First_x = 0, First_y = 0; //스테이지 시작 시 최초의 캐릭터 x,y  위치
char mapData[MAP_X_MAX * MAP_Y_MAX];

void mainscreen();
void print_block(int x, int y, int state);
typedef struct rank {
	int score;
	char name[4];
	struct rank* find;
}ranking;
void rankswap(ranking* n1, ranking* n2) {
	ranking rs;
	rs = *n2;
	*n2 = *n1;
	*n1 = rs;
}
void gotoxy(int x, int y) {
	COORD pos = { x,y };
	SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), pos);
}
void setColor(unsigned short text, unsigned short back) {
	SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), text | (back << 4));
}

int array_compare(int a[][15], int b[][15]) {
	int i, j;
	for (i = 0; i < 13; i++)
		for (j = 0; j < 15; j++)
			if (a[i][j] != b[i][j])
				return 0;
	return 1;
}
void samearray(int a[][15], int b[][15]) {
	int i, j;
	for (i = 0; i < 13; i++)
		for (j = 0; j < 15; j++)
			b[i][j] = a[i][j];
}

char character[3][3] = { {'(','"', ')'},
						{'/','U','\\'},
						{'l','-','l'} }; //캐릭터
char bomb[3][3] = { {'  ','_', ' '},
						{'(','O',')'},
						{' ',' -',' '} }; //폭탄
char mob[3][3] = { {'(','-', ')'},
						{'l','v','l'},
						{'v','-','v'} };//몬스터
typedef struct boss {
	int heart;
	int x;
	int y;
	int direction;
	int stop;
	int invincibility_time;
	int clear;
}boss;
// 몬스터 연결리스트 관련 함수
typedef struct monster {
	int heart;
	int x;
	int y;
	int direction;
	int stop;
	int invincibility_time;
}monster;
typedef struct monster ElementM;
typedef struct ListNodeM {
	ElementM data;
	struct ListNodeM* link;
}NodeM;
typedef struct {
	NodeM* head;
}LinkListM;
void init_listM(LinkListM* LL) {
	LL->head = NULL;
}
int is_emptyM(LinkListM* LL) {
	return LL->head == NULL;
}
NodeM* get_entryM(LinkListM* LL, int pos) {
	NodeM* p = LL->head;
	int i;
	for (i = 0; i < pos; i++, p = p->link)
		if (p == NULL) return NULL;
	return p;
}
int sizeM(LinkListM* LL) {
	NodeM* p;
	int count = 0;
	for (p = LL->head; p != NULL; p = p->link) count++;
	return count;
}
int findM(LinkListM* LL, ElementM e) {
	NodeM* p;
	int pos = 0;
	for (p = LL->head; p != NULL; p = p->link) {
		if ((p->data.x == e.x) && (p->data.y == e.y)) return pos;
		pos++;
	}
	return NULL;
}
void replaceM(LinkListM* LL, int pos, ElementM e) {
	NodeM* node = get_entryM(LL, pos);
	if (node != NULL)
		node->data = e;
}
void insert_nextM(NodeM* before, NodeM* node) {
	if (node != NULL) {
		node->link = before->link;
		before->link = node;
	}
}
void insertM(LinkListM* LL, int pos, ElementM e) {
	NodeM* new_node, * prev;

	new_node = (NodeM*)malloc(sizeof(NodeM));
	new_node->data = e;
	new_node->link = NULL;

	if (pos == 0) {
		new_node->link = LL->head;
		LL->head = new_node;
	}
	else {
		prev = get_entryM(LL, pos - 1);
		if (prev != NULL)
			insert_nextM(prev, new_node);
		else free(new_node);
	}
}
NodeM* remove_nextM(NodeM* before) {
	NodeM* removed = before->link;
	if (removed != NULL)
		before->link = removed->link;
	return removed;
}
void deleteM(LinkListM* LL, int pos) {
	NodeM* prev, * removed;

	if (pos == 0 && is_emptyM(LL) == 0) {
		removed = LL->head;
		LL->head = LL->head->link;
		free(removed);
	}
	else {
		prev = get_entryM(LL, pos - 1);
		if (prev != NULL) {
			removed = remove_nextM(prev);
			free(removed);
		}
	}
}

//폭탄 연결 리스트 관련 함수
typedef struct bomb {
	int x;
	int y;
	int boom_time;
}boom;
typedef boom Element;
typedef struct ListNode {
	Element data;
	struct ListNode* link;
}Node;
typedef struct {
	Node* head;
}LinkList;
void init_list(LinkList* LL) {
	LL->head = NULL;
}
void error(char* str) {
	fprintf(stderr, "%s\n", str);
	exit(1);
}
int is_empty(LinkList* LL) {
	return LL->head == NULL;
}
Node* get_entry(LinkList* LL, int pos) {
	Node* p = LL->head;
	int i;
	for (i = 0; i < pos; i++, p = p->link)
		if (p == NULL) return NULL;
	return p;
}
int size(LinkList* LL) {
	Node* p;
	int count = 0;
	for (p = LL->head; p != NULL; p = p->link) count++;
	return count;
}
int find(LinkList* LL, Element e) {
	Node* p;
	int pos = 0;
	for (p = LL->head; p != NULL; p = p->link) {
		if ((p->data.x == e.x) && (p->data.y == e.y)) return pos;
		pos++;
	}
	return NULL;
}
void replace(LinkList* LL, int pos, Element e) {
	Node* node = get_entry(LL, pos);
	if (node != NULL)
		node->data = e;
}
void insert_next(Node* before, Node* node) {
	if (node != NULL) {
		node->link = before->link;
		before->link = node;
	}
}
void insert(LinkList* LL, int pos, Element e) {
	Node* new_node, * prev;

	new_node = (Node*)malloc(sizeof(Node));
	new_node->data = e;
	new_node->link = NULL;

	if (pos == 0) {
		new_node->link = LL->head;
		LL->head = new_node;
	}
	else {
		prev = get_entry(LL, pos - 1);
		if (prev != NULL)
			insert_next(prev, new_node);
		else free(new_node);
	}
}
Node* remove_next(Node* before) {
	Node* removed = before->link;
	if (removed != NULL)
		before->link = removed->link;
	return removed;
}
void delete(LinkList* LL, int pos) {
	Node* prev, * removed;

	if (pos == 0 && is_empty(LL) == 0) {
		removed = LL->head;
		LL->head = LL->head->link;
		free(removed);
	}
	else {
		prev = get_entry(LL, pos - 1);
		if (prev != NULL) {
			removed = remove_next(prev);
			free(removed);
		}
	}
}
void clear_list(LinkList* LL) {
	while (is_empty(LL) == 0)
		delete(LL, 0);
}

void deprint_character(int x, int y) {
	int i, j;
	for (i = x; i < x + 3; i++)
		for (j = y; j < y + 3; j++) {
			gotoxy(i, j);
			printf(" ");
		}
}//캐릭터 지우기
void deprint_boss(boss b) {
	int i;
	setColor(15, 15);
	for (i = 0; i < 9; i++) {
		gotoxy(b.x - 4, b.y - 4 + i);
		printf("         ");
	}
}
void print_character(int x, int y, int state) { //상태 0  기본, 밑 1 왼쪽 2 위 3 오른쪽
	int i, j;
	setColor(13, 15);
	if (blockstate[y / 3][x / 3] == 0)
		setColor(13, 15);
	else if (blockstate[y / 3][x / 3] == 6)
		setColor(13, 1);
	else if (blockstate[y / 3][x / 3] == 10)
		setColor(13, 14);
	else if (blockstate[y / 3][x / 3] == 12)
		setColor(13, 12);

	for (i = x; i < x + 3; i++)
		for (j = y; j < y + 3; j++) {
			gotoxy(i, j);
			printf("%c", character[j - y][i - x]);
		}
	if (state == 1) {
		gotoxy(x, y);
		printf("{'");
		gotoxy(x, y + 2);
		printf("/");
	}
	if (state == 2) {
		gotoxy(x + 1, y);
		printf(" ");
	}
	if (state == 3) {
		gotoxy(x + 1, y);
		printf("'}");
		gotoxy(x + 2, y + 2);
		printf("\\");
	}
}//캐릭터 그리기
void print_bomb(int x, int y) {
	int i, j;
	setColor(9, 15);
	for (i = x; i < x + 3; i++)
		for (j = y; j < y + 3; j++) {
			gotoxy(i, j);
			printf("%c", bomb[j - y][i - x]);
		}
}//폭탄 그리기
void print_monster(int x, int y, int heart) {
	int i, j;
	if (heart == 1)
		setColor(10, 15);
	else if (heart == 2)
		setColor(2, 15);
	for (i = x; i < x + 3; i++)
		for (j = y; j < y + 3; j++) {
			gotoxy(i, j);
			printf("%c", mob[j - y][i - x]);
		}
}//몬스터 그리기
void printMonster(LinkListM* monster_num) {
	int i, n = sizeM(monster_num);
	monster m;
	for (i = 0; i < n; i++) {
		print_monster(get_entryM(monster_num, i)->data.x, get_entryM(monster_num, i)->data.y,
			get_entryM(monster_num, i)->data.heart);
	}
}//print_monster 함수를 사용해 연결리스트에 있는 모든 몬스터를 그린다.
void print_boss(boss* b) {
	setColor(6, 14);
	if (b->heart < 5)
		setColor(14, 10);
	if (b->heart == 0) {
		b->clear = 1;
		b->heart--;
		score += 2000;
	}
	gotoxy(b->x - 4, b->y - 4);
	printf("\u250D\u2501\u2501\u2501\u2501\u2501\u2501\u2511 ");
	gotoxy(b->x - 4, b->y - 3);
	printf("\u2503\u2501\u2501\u2501K\u2501\u2501\u2503 ");
	gotoxy(b->x - 4, b->y - 2);
	printf("\u2503 \u25C6 \u25C6\u2503 ");
	gotoxy(b->x - 4, b->y - 1);
	printf("\u2503      \u2503 ");
	gotoxy(b->x - 4, b->y);
	printf("\u2503  \u250D\u2511  \u2503 ");
	gotoxy(b->x - 4, b->y + 1);
	printf("\u2503\u2501\u2501\u2501\u2501\u2501\u2501\u2503 ");
	gotoxy(b->x - 4, b->y + 2);
	printf("\u2503 \u3014-\u3015\u2503 ");
	gotoxy(b->x - 4, b->y + 3);
	printf("\u2503\u2501\u2501\u2501\u2501\u2501\u2501\u2503 ");
	gotoxy(b->x - 4, b->y + 4);
	printf("\u2228\u2228-\u2228\u2228");
}
void moveboss(boss* b, int time) {
	if ((blockstate[((b->y + 1) / 3) - 1][((b->x + 1) / 3) - 1] == 6) || (blockstate[((b->y + 1) / 3) - 1][(b->x + 1) / 3] == 6) || (blockstate[((b->y + 1) / 3) - 1][((b->x + 1) / 3) + 1] == 6) ||
		(blockstate[(b->y + 1) / 3][((b->x + 1) / 3) - 1] == 6) || (blockstate[(b->y + 1) / 3][(b->x + 1) / 3] == 6) || (blockstate[(b->y + 1) / 3][((b->x + 1) / 3) + 1] == 6) ||
		(blockstate[((b->y + 1) / 3) + 1][((b->x + 1) / 3) - 1] == 6) || (blockstate[((b->y + 1) / 3) + 1][(b->x + 1) / 3] == 6) || (blockstate[((b->y + 1) / 3) + 1][((b->x + 1) / 3) + 1] == 6)) {
		if (b->invincibility_time == 0) {
			b->invincibility_time = time + 2;
			b->heart--;
			score += 100;
		}
	}
	if (b->invincibility_time == time)
		b->invincibility_time = 0;
	if (b->direction == 1 && b->stop == 0) {
		deprint_boss(*b);
		b->x++;
		b->stop = 2;
	}
	if (b->direction == 2 && b->stop == 0) {
		deprint_boss(*b);
		b->y++;
		b->stop = 2;
	}
	if (b->direction == 3 && b->stop == 0) {
		deprint_boss(*b);
		b->x--;
		b->stop = 2;
	}
	if (b->direction == 4 && b->stop == 0) {
		deprint_boss(*b);
		b->y--;
		b->stop = 2;
	}
	if (time > 10 && time < 20 && b->x < 37) {
		b->direction = 1;
		b->stop--;
		if (b->stop < 0)
			b->stop = 0;
	}
	else if (time > 25 && time < 35 && b->x > 22) {
		print_block(33, 3, blockstate[1][11]);
		print_block(33, 6, blockstate[2][11]);
		print_block(33, 9, blockstate[3][11]);
		print_block(36, 9, blockstate[3][12]);
		b->direction = 3;
		b->stop--;
		if (b->stop < 0)
			b->stop = 0;
	}
	else if (time > 40 && time < 50 && b->x > 7) {
		b->direction = 3;
		b->stop--;
		if (b->stop < 0)
			b->stop = 0;
	}
	else if (time > 55 && time < 60 && b->y < 19) {
		print_block(9, 3, blockstate[1][3]);
		print_block(9, 6, blockstate[2][3]);
		print_block(9, 9, blockstate[3][3]);
		print_block(6, 9, blockstate[3][2]);
		b->direction = 2;
		b->stop--;
		if (b->stop < 0)
			b->stop = 0;
	}
	else if (time > 65 && time < 75 && b->x < 37) {
		print_block(9, 21, blockstate[7][3]);
		print_block(33, 21, blockstate[7][11]);
		b->direction = 1;
		b->stop--;
		if (b->stop < 0)
			b->stop = 0;
	}
	else if (time > 80 && time < 85 && b->y < 28) {
		print_block(33, 21, blockstate[7][11]);
		b->direction = 2;
		b->stop--;
		if (b->stop < 0)
			b->stop = 0;
	}
	else if (time > 90 && time < 100 && b->x > 7) {
		print_block(33, 21, blockstate[7][11]);
		print_block(33, 30, blockstate[10][11]);
		print_block(36, 24, blockstate[8][12]);
		print_block(30, 24, blockstate[8][10]);
		print_block(21, 27, blockstate[9][7]);
		print_block(18, 30, blockstate[10][6]);
		print_block(24, 30, blockstate[10][8]);
		print_block(12, 21, blockstate[7][4]);
		b->direction = 3;
		b->stop--;
		if (b->stop < 0)
			b->stop = 0;
	}
	else if (time > 105 && time < 110 && b->x < 22) {
		print_block(12, 21, blockstate[7][4]);
		print_block(6, 24, blockstate[8][2]);
		print_block(12, 24, blockstate[8][4]);
		print_block(9, 30, blockstate[10][3]);
		b->direction = 1;
		b->stop--;
		if (b->stop < 0)
			b->stop = 0;
	}
	else if (time > 110 && time < 120 && b->y > 7) {
		print_block(21, 27, blockstate[9][7]);
		print_block(18, 30, blockstate[10][6]);
		print_block(24, 30, blockstate[10][8]);
		b->direction = 4;
		b->stop--;
		if (b->stop < 0)
			b->stop = 0;
	}
	else
		b->direction = 0;

}
void boss_attack(int x, int y, int type) {
	if (blockstate[y - 1][x - 1] == 0 || blockstate[y - 1][x - 1] == 10 || blockstate[y - 1][x - 1] == 12)
		blockstate[y - 1][x - 1] = type;
	if (blockstate[y - 1][x] == 0 || blockstate[y - 1][x] == 10 || blockstate[y - 1][x] == 12)
		blockstate[y - 1][x] = type;
	if (blockstate[y - 1][x + 1] == 0 || blockstate[y - 1][x + 1] == 10 || blockstate[y - 1][x + 1] == 12)
		blockstate[y - 1][x + 1] = type;
	if (blockstate[y][x - 1] == 0 || blockstate[y][x - 1] == 10 || blockstate[y][x - 1] == 12)
		blockstate[y][x - 1] = type;
	if (blockstate[y][x] == 0 || blockstate[y][x] == 10 || blockstate[y][x] == 12)
		blockstate[y][x] = type;
	if (blockstate[y][x + 1] == 0 || blockstate[y][x + 1] == 10 || blockstate[y][x + 1] == 12)
		blockstate[y][x + 1] = type;
	if (blockstate[y + 1][x - 1] == 0 || blockstate[y + 1][x - 1] == 10 || blockstate[y + 1][x - 1] == 12)
		blockstate[y + 1][x - 1] = type;
	if (blockstate[y + 1][x] == 0 || blockstate[y + 1][x] == 10 || blockstate[y + 1][x] == 12)
		blockstate[y + 1][x] = type;
	if (blockstate[y + 1][x + 1] == 0 || blockstate[y + 1][x + 1] == 10 || blockstate[y + 1][x + 1] == 12)
		blockstate[y + 1][x + 1] = type;
}
void boss_pattern(boss* b, int time) {
	if (time == 5)
		boss_attack(7, 7, 10);
	if (time == 7)
		boss_attack(7, 7, 12);
	if (time == 8)
		boss_attack(7, 7, 0);
	if (time == 15)
		boss_attack(12, 7, 10);
	if (time == 17)
		boss_attack(12, 7, 12);
	if (time == 18)
		boss_attack(12, 7, 0);
	if (time == 30) {
		boss_attack(7, 7, 10);
		boss_attack(11, 5, 10);
		boss_attack(3, 5, 10);
	}
	if (time == 32) {
		boss_attack(7, 7, 12);
		boss_attack(11, 5, 12);
		boss_attack(3, 5, 12);
	}
	if (time == 33) {
		boss_attack(7, 7, 0);
		boss_attack(11, 5, 0);
		boss_attack(3, 5, 0);
	}
	if (time == 45)
		boss_attack(2, 7, 10);
	if (time == 47)
		boss_attack(2, 7, 12);
	if (time == 48)
		boss_attack(2, 7, 0);
	if (time == 70) {
		boss_attack(7, 2, 10);
		boss_attack(7, 6, 10);
		boss_attack(7, 10, 10);
	}
	if (time == 72) {
		boss_attack(7, 2, 12);
		boss_attack(7, 6, 12);
		boss_attack(7, 10, 12);
	}
	if (time == 73) {
		boss_attack(7, 2, 0);
		boss_attack(7, 6, 0);
		boss_attack(7, 10, 0);
	}
	if (time == 90) {
		boss_attack(7, 2, 10);
		boss_attack(7, 6, 10);
		boss_attack(7, 10, 10);
		boss_attack(12, 6, 10);
		boss_attack(2, 6, 10);
	}
	if (time == 92) {
		boss_attack(7, 2, 12);
		boss_attack(7, 6, 12);
		boss_attack(7, 10, 12);
		boss_attack(12, 6, 12);
		boss_attack(2, 6, 12);
	}
	if (time == 93) {
		boss_attack(7, 2, 0);
		boss_attack(7, 6, 0);
		boss_attack(7, 10, 0);
		boss_attack(12, 6, 0);
		boss_attack(2, 6, 0);
	}
	if (time == 102) {
		boss_attack(1, 1, 10);
		boss_attack(1, 11, 10);
		boss_attack(13, 1, 10);
		boss_attack(13, 11, 10);
		boss_attack(7, 6, 10);
	}
	if (time == 104) {
		boss_attack(1, 1, 12);
		boss_attack(1, 11, 12);
		boss_attack(13, 1, 12);
		boss_attack(13, 11, 12);
		boss_attack(7, 6, 12);
	}
	if (time == 105) {
		boss_attack(1, 1, 0);
		boss_attack(1, 11, 0);
		boss_attack(13, 1, 0);
		boss_attack(13, 11, 0);
		boss_attack(7, 6, 0);
	}
	if (time == 110) {
		boss_attack(7, 2, 10);
		boss_attack(7, 6, 10);
		boss_attack(7, 10, 10);
		boss_attack(12, 6, 10);
		boss_attack(2, 6, 10);
	}
	if (time == 112) {
		boss_attack(7, 2, 12);
		boss_attack(7, 6, 12);
		boss_attack(7, 10, 12);
		boss_attack(12, 6, 12);
		boss_attack(2, 6, 12);
	}
	if (time == 113) {
		boss_attack(7, 2, 0);
		boss_attack(7, 6, 0);
		boss_attack(7, 10, 0);
		boss_attack(12, 6, 0);
		boss_attack(2, 6, 0);
	}
	if (time == 115) {
		boss_attack(1, 1, 10);
		boss_attack(1, 11, 10);
		boss_attack(13, 1, 10);
		boss_attack(13, 11, 10);
		boss_attack(7, 6, 10);
	}
	if (time == 117) {
		boss_attack(1, 1, 12);
		boss_attack(1, 11, 12);
		boss_attack(13, 1, 12);
		boss_attack(13, 11, 12);
		boss_attack(7, 6, 12);
	}
	if (time == 119) {
		boss_attack(1, 1, 0);
		boss_attack(1, 11, 0);
		boss_attack(13, 1, 0);
		boss_attack(13, 11, 0);
		boss_attack(7, 6, 0);
	}
}
void monster_death(LinkListM* LL) {
	NodeM* p1;
	int del_1 = 0;
	p1 = LL->head;
	while (p1 != NULL) {
		if (p1->data.heart == 0) {
			deprint_character(p1->data.x, p1->data.y);
			deleteM(LL, findM(LL, p1->data));
			if (sizeM(LL) == 0)
				break;
			p1 = LL->head;
		}
		p1 = p1->link;
	}

}
void moveMonster(LinkListM* monster_num, int m_time, int stage_num) {
	int i, n = sizeM(monster_num);
	monster m;
	for (i = 0; i < n; i++) {
		if (blockstate[(get_entryM(monster_num, i)->data.y + 1) / 3][(get_entryM(monster_num, i)->data.x + 1) / 3] == 6 && get_entryM(monster_num, i)->data.invincibility_time == 0) {
			get_entryM(monster_num, i)->data.heart -= 1;
			score += 100;
			get_entryM(monster_num, i)->data.invincibility_time = m_time + 2;
		}
		if (get_entryM(monster_num, i)->data.invincibility_time == m_time)
			get_entryM(monster_num, i)->data.invincibility_time = 0;
		if (get_entryM(monster_num, i)->data.stop == 0) {
			if (get_entryM(monster_num, i)->data.direction == 0) {
				if (get_entryM(monster_num, i)->data.x < MAP_X_MAX - 3 &&
					blockstate[get_entryM(monster_num, i)->data.y / 3][(get_entryM(monster_num, i)->data.x + 3) / 3] % 2 == 0 &&
					blockstate[(get_entryM(monster_num, i)->data.y + 2) / 3][(get_entryM(monster_num, i)->data.x + 3) / 3] % 2 == 0) {
					deprint_character(get_entryM(monster_num, i)->data.x, get_entryM(monster_num, i)->data.y);
					get_entryM(monster_num, i)->data.x += 1;
					get_entryM(monster_num, i)->data.stop = M_S_C;
				}
				else {
					get_entryM(monster_num, i)->data.direction = 1;
					if (stage_num == 3)
						get_entryM(monster_num, i)->data.direction = 2;
				}

			}

			if (get_entryM(monster_num, i)->data.direction == 1) {
				if (get_entryM(monster_num, i)->data.y < MAP_Y_MAX - 3 &&
					blockstate[((get_entryM(monster_num, i)->data.y) / 3) + 1][get_entryM(monster_num, i)->data.x / 3] % 2 == 0 &&
					blockstate[((get_entryM(monster_num, i)->data.y) / 3) + 1][(get_entryM(monster_num, i)->data.x + 2) / 3] % 2 == 0) {
					deprint_character(get_entryM(monster_num, i)->data.x, get_entryM(monster_num, i)->data.y);
					get_entryM(monster_num, i)->data.y += 1;
					get_entryM(monster_num, i)->data.stop = M_S_C;
				}
				else
					get_entryM(monster_num, i)->data.direction = 2;
			}
			if (get_entryM(monster_num, i)->data.direction == 2) {
				if (get_entryM(monster_num, i)->data.x > 0 &&
					blockstate[get_entryM(monster_num, i)->data.y / 3][(get_entryM(monster_num, i)->data.x - 1) / 3] % 2 == 0 &&
					blockstate[(get_entryM(monster_num, i)->data.y + 2) / 3][(get_entryM(monster_num, i)->data.x - 1) / 3] % 2 == 0) {
					deprint_character(get_entryM(monster_num, i)->data.x, get_entryM(monster_num, i)->data.y);
					get_entryM(monster_num, i)->data.x -= 1;
					get_entryM(monster_num, i)->data.stop = M_S_C;
				}
				else {
					get_entryM(monster_num, i)->data.direction = 3;
					if (stage_num == 3)
						get_entryM(monster_num, i)->data.direction = 0;
				}

			}

			if (get_entryM(monster_num, i)->data.direction == 3) {
				if (get_entryM(monster_num, i)->data.y > 0 &&
					blockstate[((get_entryM(monster_num, i)->data.y + 2) / 3) - 1][get_entryM(monster_num, i)->data.x / 3] % 2 == 0 &&
					blockstate[((get_entryM(monster_num, i)->data.y + 2) / 3) - 1][(get_entryM(monster_num, i)->data.x + 2) / 3] % 2 == 0) {
					deprint_character(get_entryM(monster_num, i)->data.x, get_entryM(monster_num, i)->data.y);
					get_entryM(monster_num, i)->data.y -= 1;
					get_entryM(monster_num, i)->data.stop = M_S_C;
				}
				else
					get_entryM(monster_num, i)->data.direction = 0;
			}
		}
		else
			get_entryM(monster_num, i)->data.stop -= 1;
	}

}
int hit_monster(int x, int y, LinkListM* LL) {
	NodeM* p1;
	int hit = 0;
	p1 = LL->head;
	while (p1 != NULL) {
		if (x + 2 > p1->data.x && y + 2 > p1->data.y && x < p1->data.x + 2 && y < p1->data.y + 2)
			hit = 1;
		p1 = p1->link;
	}
	return hit;
}
int bomb_explosion(LinkList* LL, int x, int y, int power, int time) {
	int i;
	boom b;
	blockstate[y][x] = 6;
	for (i = x + 1; i <= x + power; i++) {
		if (i >= 15)
			break;
		if (blockstate[y][i] == 0)
			blockstate[y][i] = 6;
		else if (blockstate[y][i] == 1) {
			switch (rand() % 8) {
			case 0:
				blockstate[y][i] = 2;
				break;
			case 1:
				blockstate[y][i] = 4;
				break;
			case 2:
				blockstate[y][i] = 8;
				break;
			default:
				blockstate[y][i] = 0;
				break;
			}
			break;
		}
		else if (blockstate[y][i] == 5) {
			b.x = i;
			b.y = y;
			delete(LL, find(LL, b));
			bomb_explosion(LL, i, y, power, time);
			break;
		}
		else
			break;
	}
	for (i = x - 1; i >= x - power; i--) {
		if (i < 0)
			break;
		if (blockstate[y][i] % 2 == 0)
			blockstate[y][i] = 6;
		else if (blockstate[y][i] == 1) {
			switch (rand() % 8) {
			case 0:
				blockstate[y][i] = 2;
				break;
			case 1:
				blockstate[y][i] = 4;
				break;
			case 2:
				blockstate[y][i] = 8;
				break;
			default:
				blockstate[y][i] = 0;
				break;
			}
			break;
		}
		else if (blockstate[y][i] == 5) {
			b.x = i;
			b.y = y;
			delete(LL, find(LL, b));
			bomb_explosion(LL, i, y, power, time);
			break;
		}
		else
			break;
	}
	for (i = y + 1; i <= y + power; i++) {
		if (i > 13)
			break;
		if (blockstate[i][x] % 2 == 0)
			blockstate[i][x] = 6;
		else if (blockstate[i][x] == 1) {
			switch (rand() % 8) {
			case 0:
				blockstate[i][x] = 2;
				break;
			case 1:
				blockstate[i][x] = 4;
				break;
			case 2:
				blockstate[i][x] = 8;
				break;
			default:
				blockstate[i][x] = 0;
				break;
			}
			break;
		}
		else if (blockstate[i][x] == 5) {
			b.x = x;
			b.y = i;
			delete(LL, find(LL, b));
			bomb_explosion(LL, x, i, power, time);
			break;
		}
		else
			break;
	}
	for (i = y - 1; i >= y - power; i--) {
		if (i < 0)
			break;
		if (blockstate[i][x] % 2 == 0)
			blockstate[i][x] = 6;
		else if (blockstate[i][x] == 1) {
			switch (rand() % 8) {
			case 0:
				blockstate[i][x] = 2;
				break;
			case 1:
				blockstate[i][x] = 4;
				break;
			case 2:
				blockstate[i][x] = 8;
				break;
			default:
				blockstate[i][x] = 0;
				break;
			}
			break;
		}
		else if (blockstate[i][x] == 5) {
			b.x = x;
			b.y = i;
			delete(LL, find(LL, b));
			bomb_explosion(LL, x, i, power, time);
			break;
		}
		else
			break;
	}
	return time + 1;
}//폭탄 폭발

void print_block(int x, int y, int state) {
	if (state == 1)
		setColor(14, 4);
	if (state == 3)
		setColor(3, 7);
	if (state == 10) {
		setColor(1, 14);
		gotoxy(x, y);
		printf("   ");
		gotoxy(x, y + 1);
		printf("   ");
		gotoxy(x, y + 2);
		printf("   ");
	}
	if (state == 12) {
		setColor(1, 12);
		gotoxy(x, y);
		printf("   ");
		gotoxy(x, y + 1);
		printf("   ");
		gotoxy(x, y + 2);
		printf("   ");
	}
	if (state == 0) {
		setColor(1, 15);
		gotoxy(x, y);
		printf("   ");
		gotoxy(x, y + 1);
		printf("   ");
		gotoxy(x, y + 2);
		printf("   ");
	}
	if (state % 2 == 1) {
		gotoxy(x, y);
		printf("┏━┓");
		gotoxy(x, y + 1);
		printf("┃ ┃");
		gotoxy(x, y + 2);
		printf("┗━┛");
	}
	if (state == 2) {
		setColor(11, 15);
		gotoxy(x, y);
		printf("● ");
		gotoxy(x, y + 1);
		printf(" ●");
		gotoxy(x, y + 2);
		printf("● ");
	}
	if (state == 4) {
		setColor(11, 15);
		gotoxy(x, y);
		printf("■ ");
		gotoxy(x, y + 1);
		printf(" ■");
		gotoxy(x, y + 2);
		printf("■ ");
	}
	if (state == 5)
		print_bomb(x, y);
	if (state == 6) {
		setColor(1, 1);
		gotoxy(x, y);
		printf("   ");
		gotoxy(x, y + 1);
		printf("   ");
		gotoxy(x, y + 2);
		printf("   ");
	}
	if (state == 8) {
		setColor(5, 15);
		gotoxy(x, y);
		printf("┏━┓");
		gotoxy(x, y + 1);
		printf("┃B┃");
		gotoxy(x, y + 2);
		printf("┗━┛");
	}

}
void print_heart() {
	int i;
	gotoxy(49, 4);
	setColor(12, 0);
	printf("       ");
	gotoxy(49, 4);
	for (i = 0; i < heart; i++)
		printf("♥");
}
void print_power(int count, int power) {
	int i;
	setColor(15, 0);
	gotoxy(50, 10);
	printf("Count");
	gotoxy(46, 12);
	setColor(9, 0);
	for (i = 0; i < count; i++)
		printf("●");
	for (i = 0; i < 6 - count; i++)
		printf("○");
	setColor(15, 0);
	gotoxy(50, 14);
	printf("Power");
	gotoxy(46, 16);
	setColor(9, 0);
	for (i = 0; i < power; i++)
		printf("■");
	for (i = 0; i < 6 - power; i++)
		printf("□");
}
void trace_clear() {
	int i, j;
	for (i = 0; i < 15; i++)
		for (j = 0; j < 13; j++)
			if (blockstate[j][i] == 6)
				blockstate[j][i] = 0;
}

void SetMap() {
	int i, j;
	for (i = 0; i < 15; i++)
		for (j = 0; j < 13; j++) {
			print_block(i * 3, j * 3, blockstate[j][i]);
		}
}
void Setbomb() {
	int i, j;
	for (i = 0; i < 15; i++)
		for (j = 0; j < 13; j++) {
			if (blockstate[j][i] == 5)
				print_bomb(i * 3, j * 3);
			if (blockstate[j][i] == 6) {
				setColor(9, 9);
				gotoxy(i * 3, j * 3);
				printf("   ");
				gotoxy(i * 3, (j * 3) + 1);
				printf("   ");
				gotoxy(i * 3, (j * 3) + 2);
				printf("   ");
			}
		}
}
boss SetBoss(boss b) {
	b.x = 22;
	b.y = 7;
	b.direction = 0;
	b.stop = 0;
	b.invincibility_time = 0;
	b.heart = 15;
	b.clear = 0;
	return b;
}
void SetMonster(int Stage_num, LinkListM* monster_num) {
	int i, n;
	FILE* f;
	monster m;
	init_listM(monster_num);
	if (Stage_num == 1)
		f = fopen("mob1.txt", "r");
	else if (Stage_num == 2)
		f = fopen("mob2.txt", "r");
	else if (Stage_num == 3)
		f = fopen("mob3.txt", "r");
	fscanf(f, "%d", &n);
	for (i = 0; i < n; i++) {
		fscanf(f, "%d", &m.x);
		m.x *= 3;
		fscanf(f, "%d", &m.y);
		m.y *= 3;
		fscanf(f, "%d", &m.heart);
		m.direction = 0;
		if (Stage_num == 3 && i > 2)
			m.direction = 2;
		m.stop = M_S_C;
		m.invincibility_time = 0;
		insertM(monster_num, 0, m);
	}
}
void SetStage(int Stage_num) {
	int i, j;
	FILE* f;
	if (Stage_num == 1)
		f = fopen("map1.txt", "r");
	else if (Stage_num == 2)
		f = fopen("map2.txt", "r");
	else if (Stage_num == 3)
		f = fopen("map3.txt", "r");
	for (i = 0; i < 13; i++)
	{
		for (j = 0; j < 15; j++)
		{
			fscanf(f, "%d", &blockstate[i][j]);
			blockstate2[i][j] = blockstate[i][j];
		}
		puts("");
	}
	fscanf(f, "%d", &First_x);
	fscanf(f, "%d", &First_y);
	fclose(f);
}
void SetConsole() {

	system("title game test");
	system("mode con:cols=60 lines=39");

	HANDLE hConsole;
	CONSOLE_CURSOR_INFO ConsoleCursor;
	hConsole = GetStdHandle(STD_OUTPUT_HANDLE);

	ConsoleCursor.bVisible = 0;
	ConsoleCursor.dwSize = 1;

	SetConsoleCursorInfo(hConsole, &ConsoleCursor);

	COORD Pos = { 0, 0 };
	SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), Pos);
}

void endscreen() {
	POINT mouse;
	HWND hWnd;
	FILE* f;
	f = fopen("rank.txt", "r");
	ranking rank[10], now;
	int i, j, count = 0, n = score, ranking_now, select = 0;
	while (n != 0) {
		n = n / 10;
		++count;
	}
	fscanf(f, "%d", &ranking_now);
	if (ranking_now != 0)
		for (i = 0; i < ranking_now; i++) {
			fscanf(f, "%s", &rank[i].name);
			fscanf(f, "%d", &rank[i].score);
		}
	fclose(f);
	setColor(15, 0);
	system("cls");
	gotoxy(26, 15);
	printf("Score");
	gotoxy(28 - (count / 2), 17);
	printf("%d", score);
	gotoxy(21, 23);
	printf("Input Your Name");
	gotoxy(26, 25);
	printf("____");
	gotoxy(26, 25);
	scanf("%s", &now.name);
	now.score = score;
	if (ranking_now == 0) {
		f = fopen("rank.txt", "w");
		fprintf(f, "1\n");
		fprintf(f, "%s", now.name);
		fprintf(f, " %d\n", now.score);
		fclose(f);
	}
	else {
		if (now.score > rank[ranking_now - 1].score) {
			f = fopen("rank.txt", "w");
			strcpy(rank[ranking_now].name, now.name);
			rank[ranking_now].score = now.score;
			for (i = ranking_now; i > 0; i--)
				if (rank[i].score > rank[i - 1].score)
					rankswap(&rank[i], &rank[i - 1]);
			if (ranking_now == 10)
				fprintf(f, "10\n");
			else
				fprintf(f, "%d\n", ranking_now + 1);
			for (i = 0; i < ranking_now + 1; i++) {
				fprintf(f, "%s", rank[i].name);
				fprintf(f, " %d\n", rank[i].score);
				fclose(f);
			}
		}
		else if (now.score <= rank[ranking_now - 1].score && ranking_now < 10) {
			f = fopen("rank.txt", "w");
			fprintf(f, "%d\n", ranking_now + 1);
			for (i = 0; i < ranking_now; i++) {
				fprintf(f, "%s", rank[i].name);
				fprintf(f, " %d\n", rank[i].score);
			}
			fprintf(f, "%s", now.name);
			fprintf(f, " %d\n", now.score);
			fclose(f);
		}
	}
	fclose(f);
	system("cls");
	gotoxy(20, 15);
	printf("입력되었습니다.");
	gotoxy(25, 25);
	printf("처음으로");
	gotoxy(25, 27);
	printf("게임 종료");
	while (select == 0) {
		if (GetAsyncKeyState(0x01) & 0x0001) {
			GetCursorPos(&mouse);
			hWnd = WindowFromPoint(mouse);
			ScreenToClient(hWnd, &mouse);
			if (mouse.x > 201 && mouse.x < 271 && mouse.y>199 && mouse.y < 208)
				select = 1;
			if (mouse.x > 201 && mouse.x < 271 && mouse.y>215 && mouse.y < 224)
				select = 2;
		}
	}
	if (select == 1)
		mainscreen();
	else if (select == 2)
		exit(0);
}
void howtoplay() {
	POINT mouse;
	HWND hWnd;
	int back = 0;
	system("cls");
	setColor(15, 0);
	gotoxy(0, 0);
	printf("뒤로가기");
	gotoxy(19, 11);
	printf("폭탄으로 벽을 부수고");
	gotoxy(15, 13);
	printf("몬스터에게 피해를 입혀보세요.");
	gotoxy(13, 15);
	printf("벽을 부수면 폭탄을 강화할 수 있는");
	gotoxy(16, 17);
	printf("아이템(●, ■)이 나옵니다.");
	gotoxy(19, 21);
	printf("move : ← ↑ → ↓");
	gotoxy(19, 23);
	printf("bomb : Space bar");
	while (!back) {
		if (GetAsyncKeyState(0x01) & 0x0001) {
			GetCursorPos(&mouse);
			hWnd = WindowFromPoint(mouse);
			ScreenToClient(hWnd, &mouse);
			if (mouse.x > 0 && mouse.x < 70 && mouse.y>0 && mouse.y < 9)
				back = 1;
		}
	}
	mainscreen();
}

void gamescreen(int stage_num) {
	time_t t = time(NULL), t_now, t_left = 120;
	clock_t c_now;
	LinkList* count_boom = (LinkList*)malloc(sizeof(LinkList));
	LinkListM* monster_num = (LinkListM*)malloc(sizeof(LinkListM));
	boom b;
	boss* boss1 = (boss*)malloc(sizeof(boss));
	int invincibility_time = 0, boombuffer = 0, gameover = 0, time_bonus;
	system("cls");
	SetStage(stage_num);
	init_list(count_boom);
	SetMap();
	SetMonster(stage_num, monster_num);
	if (stage_num == 3)
		*boss1 = SetBoss(*boss1);
	print_heart();
	int characterX = First_x * 3, characterY = First_y * 3, Setonemore = 0;
	int max_bomb = 1, bomb_count = 0, bomb_power = 1, bomb_time = 0, m1, m2;
	print_power(max_bomb, bomb_power);
	while (1) {
		t_now = time(NULL);
		c_now = clock();
		if (!array_compare(blockstate, blockstate2)) {
			samearray(blockstate, blockstate2);
			SetMap();
		}
		printMonster(monster_num);
		moveMonster(monster_num, (int)t_now, stage_num);
		monster_death(monster_num);
		if (stage_num == 3) {
			moveboss(boss1, (t_now - t));
			print_boss(boss1);
			boss_pattern(boss1, (t_now - t));
		}

		setColor(15, 0);
		gotoxy(52, 1);
		printf("   ");
		gotoxy(52, 1);
		printf("%d", t_left - (t_now - t));
		gotoxy(50, 6);
		printf("Score");
		gotoxy(52, 8);
		printf("%d", score);
		gotoxy(52, 19);
		printf("%d", is_emptyM(monster_num));

		setColor(13, 15);
		deprint_character(characterX, characterY);
		Setbomb();
		setColor(13, 15);
		if (invincibility_time != 0)
			setColor(5, 15);
		if (GetAsyncKeyState(0x25) != 0 && characterX > 0 &&
			blockstate[characterY / 3][(characterX - 1) / 3] % 2 == 0 && blockstate[(characterY + 2) / 3][(characterX - 1) / 3] % 2 == 0) {//왼쪽 키
			characterX -= 1;
			print_character(characterX, characterY, 1);
		}

		else if (GetAsyncKeyState(0x27) != 0 && characterX < MAP_X_MAX - 3 &&
			blockstate[characterY / 3][(characterX + 3) / 3] % 2 == 0 && blockstate[(characterY + 2) / 3][(characterX + 3) / 3] % 2 == 0) {//오른쪽 키
			characterX += 1;
			print_character(characterX, characterY, 3);
		}

		else if (GetAsyncKeyState(0x26) != 0 && characterY > 0 &&
			blockstate[((characterY + 2) / 3) - 1][characterX / 3] % 2 == 0 && blockstate[((characterY + 2) / 3) - 1][(characterX + 2) / 3] % 2 == 0) {//윗 키
			characterY -= 1;
			print_character(characterX, characterY, 2);
		}

		else if (GetAsyncKeyState(0x28) != 0 && characterY < MAP_Y_MAX - 3 &&
			blockstate[((characterY) / 3) + 1][characterX / 3] % 2 == 0 && blockstate[((characterY) / 3) + 1][(characterX + 2) / 3] % 2 == 0) { //밑 키
			characterY += 1;
			print_character(characterX, characterY, 0);
		}
		else
			print_character(characterX, characterY, 0);
		if ((GetAsyncKeyState(0x20) != 0) && blockstate[(characterY) / 3][characterX / 3] == 0 && bomb_count > 0 && boombuffer == 0) { //스페이스바
			blockstate[(characterY + 1) / 3][(characterX + 1) / 3] = 5;
			b.x = (characterX + 1) / 3;
			b.y = (characterY + 1) / 3;
			b.boom_time = ((int)t_now) + 3;
			insert(count_boom, 0, b);
			boombuffer = ((int)c_now) + 250;
		}
		if (!is_empty(count_boom))
			if (get_entry(count_boom, size(count_boom) - 1)->data.boom_time == (int)t_now) {
				blockstate[get_entry(count_boom, size(count_boom) - 1)->data.y][get_entry(count_boom, size(count_boom) - 1)->data.x] = 0;
				bomb_time = bomb_explosion(count_boom, get_entry(count_boom, size(count_boom) - 1)->data.x,
					get_entry(count_boom, size(count_boom) - 1)->data.y, bomb_power, (int)t_now);
				delete(count_boom, size(count_boom) - 1);
			}
		if (bomb_time == (int)t_now)
			trace_clear();
		bomb_count = max_bomb - size(count_boom);
		setColor(13, 15);
		if ((blockstate[(characterY + 1) / 3][(characterX + 1) / 3] == 6 || blockstate[(characterY + 1) / 3][(characterX + 1) / 3] == 12 ||
			hit_monster(characterX, characterY, monster_num) == 1) && invincibility_time == 0) {
			heart--;
			invincibility_time = (int)t_now + 2;
			print_heart();
		}
		if (blockstate[(characterY + 1) / 3][(characterX + 1) / 3] == 2) {
			blockstate[(characterY + 1) / 3][(characterX + 1) / 3] = 0;
			max_bomb++;
			if (max_bomb > 6)
				max_bomb = 6;
			print_power(max_bomb, bomb_power);
		}
		if (blockstate[(characterY + 1) / 3][(characterX + 1) / 3] == 4) {
			blockstate[(characterY + 1) / 3][(characterX + 1) / 3] = 0;
			bomb_power++;
			if (bomb_power > 6)
				bomb_power = 6;
			print_power(max_bomb, bomb_power);
		}
		if (blockstate[(characterY + 1) / 3][(characterX + 1) / 3] == 8) {
			blockstate[(characterY + 1) / 3][(characterX + 1) / 3] = 0;
			score += 50;
		}
		if (invincibility_time == (int)t_now)
			invincibility_time = 0;
		if (boombuffer < (int)c_now)
			boombuffer = 0;
		if (stage_num == 3)
			if (boss1->clear == 1)
				break;
		if (stage_num == 1 || stage_num == 2)
			if ((int)is_emptyM(monster_num) == 1)
				break;
		if (heart == 0 || t_left - (t_now - t) == 0) {
			gameover = 1;
			break;
		}

		time_bonus = (t_left - (t_now - t)) * 10;
		Sleep(50);
	}
	setColor(0, 0);
	score += time_bonus;
	if (gameover == 1)
		endscreen();
	else {
		if (stage_num == 3)
			endscreen();
		else
			gamescreen(stage_num + 1);
	}

}

void Ranking() {
	POINT mouse;
	HWND hWnd;
	FILE* f;
	ranking rank;
	int i, back = 0, n;
	f = fopen("rank.txt", "r");
	system("cls");
	setColor(15, 0);
	gotoxy(0, 0);
	printf("뒤로가기");
	fscanf(f, "%d", &n);
	for (i = 0; i < n; i++) {
		gotoxy(23, 5 + (i * 2));
		printf("%d. ", i + 1);
		fscanf(f, "%s", &rank.name);
		fscanf(f, "%d", &rank.score);
		gotoxy(26, 5 + (i * 2));
		printf("%s", rank.name);
		gotoxy(31, 5 + (i * 2));
		printf(": %d", rank.score);
	}
	fclose(f);
	while (!back) {
		if (GetAsyncKeyState(0x01) & 0x0001) {
			GetCursorPos(&mouse);
			hWnd = WindowFromPoint(mouse);
			ScreenToClient(hWnd, &mouse);
			if (mouse.x > 0 && mouse.x < 70 && mouse.y>0 && mouse.y < 9)
				back = 1;
		}
	}
	mainscreen();
}
void mainscreen() {
	POINT mouse;
	HWND hWnd;
	system("cls");
	int x = 7;
	int select = 5;
	setColor(15, 15);
	gotoxy(x + 6, 10); printf("     ");//폭
	gotoxy(x + 7, 11); printf(" "); gotoxy(x + 9, 11); printf(" ");
	gotoxy(x + 6, 12); printf("     ");
	gotoxy(x + 8, 13); printf(" ");
	gotoxy(x + 6, 14); printf("     ");
	gotoxy(x + 6, 16); printf("     ");
	gotoxy(x + 10, 17); printf(" ");
	gotoxy(x + 12, 10); printf("   "); gotoxy(x + 16, 10); printf(" "); //탄
	gotoxy(x + 12, 11); printf(" "); gotoxy(x + 16, 11); printf(" ");
	gotoxy(x + 12, 12); printf("   "); gotoxy(x + 16, 12); printf("  ");
	gotoxy(x + 12, 13); printf(" "); gotoxy(x + 16, 13); printf(" ");
	gotoxy(x + 12, 14); printf("   "); gotoxy(x + 16, 14); printf(" ");
	gotoxy(x + 12, 16); printf(" ");
	gotoxy(x + 12, 17); printf("     ");
	gotoxy(x + 19, 10); printf(" "); gotoxy(x + 21, 10); printf(" "); gotoxy(x + 23, 10); printf(" "); //벽
	gotoxy(x + 19, 11); printf("     ");
	gotoxy(x + 19, 12); printf(" "); gotoxy(x + 21, 12); printf(" "); gotoxy(x + 23, 12); printf(" ");
	gotoxy(x + 19, 13); printf(" "); gotoxy(x + 21, 13); printf("   ");
	gotoxy(x + 19, 14); printf("   "); gotoxy(x + 23, 14); printf(" ");
	gotoxy(x + 19, 16); printf("     ");
	gotoxy(x + 23, 17); printf(" ");
	gotoxy(x + 29, 10); printf(" "); gotoxy(x + 31, 10); printf(" "); //게
	gotoxy(x + 25, 11); printf("   "); gotoxy(x + 29, 11); printf(" "); gotoxy(x + 31, 11); printf(" ");
	gotoxy(x + 27, 12); printf(" "); gotoxy(x + 29, 12); printf(" "); gotoxy(x + 31, 12); printf(" ");
	gotoxy(x + 27, 13); printf("   "); gotoxy(x + 31, 13); printf(" ");
	gotoxy(x + 27, 14); printf(" "); gotoxy(x + 29, 14); printf(" "); gotoxy(x + 31, 14); printf(" ");
	gotoxy(x + 26, 15); printf(" "); gotoxy(x + 29, 15); printf(" "); gotoxy(x + 31, 15); printf(" ");
	gotoxy(x + 29, 16); printf(" "); gotoxy(x + 31, 16); printf(" ");
	gotoxy(x + 29, 17); printf(" "); gotoxy(x + 31, 17); printf(" ");
	gotoxy(x + 34, 10); printf("  "); gotoxy(x + 38, 10); printf(" "); //임
	gotoxy(x + 33, 11); printf(" "); gotoxy(x + 36, 11); printf(" "); gotoxy(x + 38, 11); printf(" ");
	gotoxy(x + 33, 12); printf(" "); gotoxy(x + 36, 12); printf(" "); gotoxy(x + 38, 12); printf(" ");
	gotoxy(x + 34, 13); printf("  "); gotoxy(x + 38, 13); printf(" ");
	gotoxy(x + 34, 15); printf("     ");
	gotoxy(x + 34, 16); printf(" "); gotoxy(x + 38, 16); printf(" ");
	gotoxy(x + 34, 17); printf("     ");

	setColor(15, 0);
	gotoxy(x + 18, 25);
	printf("게임 시작");
	gotoxy(x + 18, 27);
	printf("게임 방법");
	gotoxy(x + 18, 29);
	printf("랭킹 확인");
	gotoxy(x + 18, 31);
	printf("게임 종료");
	setColor(0, 0);
	gotoxy(59, 38);
	while (select == 5) {
		if (GetAsyncKeyState(0x01) & 0x0001) {
			GetCursorPos(&mouse);
			hWnd = WindowFromPoint(mouse);
			ScreenToClient(hWnd, &mouse);
			if (mouse.x > 201 && mouse.x < 271 && mouse.y>199 && mouse.y < 208)
				select = 1;
			if (mouse.x > 201 && mouse.x < 271 && mouse.y>215 && mouse.y < 224)
				select = 2;
			if (mouse.x > 201 && mouse.x < 271 && mouse.y>231 && mouse.y < 240)
				select = 3;
			if (mouse.x > 201 && mouse.x < 271 && mouse.y>247 && mouse.y < 256)
				select = 0;
		}
	}
	if (select == 1)
		gamescreen(1);
	else if (select == 2)
		howtoplay();
	else if (select == 3)
		Ranking();
	else {
		system("cls");
		return 0;
	}
}
void main() {
	srand(time(NULL));
	SetConsole();
	mainscreen();
	return 0;
}
