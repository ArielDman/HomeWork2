# HomeWork2

#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#define SIZE 6

typedef struct Worker {
	unsigned long ID;
	char* name;
	unsigned long salary;

	union year {
		char hebrew[SIZE];
		unsigned long international;
	} *y;
}myworker;

typedef struct WorkerList
{
	myworker* data;
	struct WorkerList* next;
} WorkerList;


myworker* CreateWorker(unsigned long id, char* name, unsigned long salary, union year* myyear, int yeartype);
void printworker(myworker* worker, int yeartype);
WorkerList* addWorker(WorkerList* head, myworker* w);
int index1(WorkerList* head, long unsigned id);
int index2(WorkerList* head, long unsigned id);
WorkerList* deleteWorstWorker(WorkerList* head);
void update_worker(WorkerList* head, float percent);
WorkerList* reverse(WorkerList* head);
void freeWorkers(WorkerList* head);


void main() {
	int yeartype = 0;
	//test data
	union year* yr = "tashpa";

	myworker* w1 = CreateWorker(123, "ariel", 4000, yr, yeartype);
	//printworker(w1, yeartype);
	myworker* w2 = CreateWorker(456, "shay", 5000, yr, yeartype);//change head
	//printworker(w2, yeartype);
	myworker* w3 = CreateWorker(789, "eden", 5500, yr, yeartype);//add tail
	//printworker(w3, yeartype);
	myworker* w4 = CreateWorker(1011, "moshe", 5600, yr, yeartype);//change mid list
	//printworker(w4, yeartype);
	myworker* w5 = CreateWorker(1213, "kiko", 100, yr, yeartype);//change mid list
	//printworker(w5, yeartype);

	WorkerList* list = (WorkerList*)malloc(sizeof(WorkerList));
	list->data = w1;
	list->next = NULL;

	list = addWorker(list, w2);
	list = addWorker(list, w3);
	list = addWorker(list, w4);
	list = addWorker(list, w5);

	////test list with index1
	printf("%s ->position: %d\n", w1->name, index1(list, w1->ID));
	printf("%s ->position: %d\n", w2->name, index1(list, w2->ID));
	printf("%s ->position: %d\n", w3->name, index1(list, w3->ID));
	printf("%s ->position: %d\n", w4->name, index1(list, w4->ID));
	printf("%s ->position: %d\n", w5->name, index1(list, w5->ID));

	////test index2
	printf(" %s ->position: %d\n", w4->name, index2(list, w4->ID));
	printf(" %s ->position: %d\n", w5->name, index2(list, w5->ID));
	printf(" %s ->position: %d\n", "5555", index2(list, 5555));

	//test deleting worker
	printf("\ncurrent least earning worker: %s", list->data->name);
	list = deleteWorstWorker(list);
	printf("\nnew head of list is: %s\n", list->data->name);

	////test updating workers
	update_worker(list, 30);
	printf("%s     $$$ = %d\n", w1->name, w1->salary);
	printf("%s     $$$ = %d\n", w2->name, w2->salary);
	printf("%s     $$$ = %d\n", w3->name, w3->salary);
	printf("%s     $$$ = %d\n", w4->name, w4->salary);
	printf("%s     $$$ = %d\n", w5->name, w5->salary);

	////test reversing order of the list
	list = reverse(list);
	printf("%s -> %d\n", w1->name, index1(list, w1->ID));
	printf("%s -> %d\n", w2->name, index1(list, w2->ID));
	printf("%s -> %d\n", w3->name, index1(list, w3->ID));
	printf("%s -> %d\n", w4->name, index1(list, w4->ID));
	printf("%s -> %d\n", w5->name, index1(list, w5->ID));


	////test freeing the list
	printf("\nbefore release:\n");
	WorkerList* tmp = list;
	while (tmp != NULL) {
		printf("%s\n", tmp->data->name);
		tmp = tmp->next;
	}
	freeWorkers(list);
	printf("\%s\n",list->data->name);

}

/*
yeartype - 0 for heb, 1 for international
*/
myworker* CreateWorker(unsigned long id, char* name, unsigned long salary, union year* myyear, int yeartype) {

	myworker* worker;
	worker = (myworker*)malloc(sizeof(myworker));
	worker->ID = id;
	worker->name = name;
	worker->salary = salary;
	if (yeartype) worker->y = myyear->international;
	else worker->y = myyear->hebrew;
	return worker;
}

void printworker(myworker* worker, int yeartype) {

	printf("the details about the worker:\n id:%u \n name:%s\n salary: %u \n",
		worker->ID, worker->name, worker->salary);
	if (yeartype) printf("year: %d", worker->y->international);
	else printf(" year: %s\n", worker->y->hebrew);
}

//add worker ordered by salary A-Z
WorkerList* addWorker(WorkerList* head, myworker* w) {
	WorkerList* new_list_member = (WorkerList*)malloc(sizeof(WorkerList));
	if (!new_list_member) {
		exit(0);
	}

	new_list_member->data = w;
	new_list_member->next = NULL;

	if (head->data->salary > w->salary) {
		new_list_member->next = head;
		return new_list_member;
	}

	WorkerList* current = head;
	WorkerList* prev = NULL;
	while (current->next != NULL)
	{
		if (current->data->salary > w->salary) {
			new_list_member->next = current;
			prev->next = new_list_member;
			return head;
		}
		prev = current;
		current = current->next;
	}

	if (current->data->salary > w->salary) {
		prev->next = new_list_member;
		new_list_member->next = current;
		return head;
	}

	current->next = new_list_member;
	return head;
}


int index1(WorkerList* head, long unsigned id) {

	int count = 1;
	WorkerList* current = head;
	while (current) {
		if (current->data->ID == id) {

			return count;
		}
		count++;
		current = current->next;
	}
	return -1;
}

int index2(WorkerList* head, long unsigned id) {

	if (head->data->ID == id) return 1; //found an item
	if (!head->next) return -1; //when no item was found and reached end of list
	int res = index2(head->next, id);
	return res == -1 ? -1 : res + 1;
}

WorkerList* deleteWorstWorker(WorkerList* head) {
	if (head->next == NULL) {
		free(head);
		return NULL;
	}
	else {
		WorkerList* current = head;
		while (current->next != NULL) {
			//do when remove first:
			if (current->data->salary < current->next->data->salary) {
				WorkerList* tmp = head;
				head = head->next;
				free(tmp);
				break;
			}

			//do when remove last
			if (current->next->next == NULL) {
				free(current->next);
				current->next = NULL;
				break;
			}
			current = current->next;
		}
	}
	return head;
}

 //assuming percent is given in the form of 100=100%
void update_worker(WorkerList* head, float percent) {
	while (head) {
		head->data->salary *= (100.0 + percent) / 100.0;
		head = head->next;
	}
}


WorkerList* reverse(WorkerList* head) {
	if (head == NULL || head->next == NULL) return head;
	WorkerList* prev = reverse(head->next);
	head->next->next = head;
	head->next = NULL;
	return prev;
}

void freeWorkers(WorkerList* head) {
	WorkerList* tmp;
	while (head != NULL) {
		tmp = head;
		head = head->next;
		free(tmp);
	}
}
