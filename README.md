#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct Node {
    char url[100];
    struct Node *prev;
    struct Node *next;
} Node;

Node *head = NULL;
Node *current = NULL;

Node* createNode(char url[]) {
    Node *newNode = (Node*)malloc(sizeof(Node));
    if (newNode == NULL) {
        printf("Memory allocation failed!\n");
        return NULL;
    }
    strcpy(newNode->url, url);
    newNode->prev = NULL;
    newNode->next = NULL;
    return newNode;
}

void deleteForwardHistory(Node *node) {
    if (node == NULL || node->next == NULL)
        return;

    Node *temp = node->next;
    Node *nextNode;

    while (temp != NULL) {
        nextNode = temp->next;
        free(temp);
        temp = nextNode;
    }

    node->next = NULL;
}

void visitPage() {
    char url[100];
    printf("Enter URL to visit: ");
    getchar();
    fgets(url, sizeof(url), stdin);
    url[strcspn(url, "\n")] = '\0';

    if (head == NULL) {
        Node *newNode = createNode(url);
        if (!newNode) return;
        head = current = newNode;
    } else {
        deleteForwardHistory(current);

        Node *newNode = createNode(url);
        if (!newNode) return;

        newNode->prev = current;
        current->next = newNode;
        current = newNode;
    }

    printf("Visited: %s\n", current->url);
}

void goBack() {
    if (current == NULL) {
        printf("No pages in history.\n");
        return;
    }
    if (current->prev == NULL) {
        printf("Already at the first page.\n");
        return;
    }

    current = current->prev;
    printf("Went back to: %s\n", current->url);
}

void goForward() {
    if (current == NULL) {
        printf("No pages in history.\n");
        return;
    }
    if (current->next == NULL) {
        printf("Already at the latest page.\n");
        return;
    }

    current = current->next;
    printf("Went forward to: %s\n", current->url);
}

void showCurrent() {
    if (current == NULL)
        printf("No current page.\n");
    else
        printf("Current Page: %s\n", current->url);
}

void showHistory() {
    if (head == NULL) {
        printf("No browsing history.\n");
        return;
    }

    Node *temp = head;
    printf("\n--- Full Browsing History ---\n");
    while (temp != NULL) {
        if (temp == current)
            printf("-> %s   [CURRENT]\n", temp->url);
        else
            printf("   %s\n", temp->url);
        temp = temp->next;
    }
    printf("-----------------------------\n");
}

void freeAll() {
    Node *temp = head;
    Node *nextNode;

    while (temp != NULL) {
        nextNode = temp->next;
        free(temp);
        temp = nextNode;
    }

    head = current = NULL;
}

int main() {
    int choice;

    do {
        printf("\n===== Browser History Manager =====\n");
        printf("1. Visit New Page\n");
        printf("2. Go Back\n");
        printf("3. Go Forward\n");
        printf("4. Show Current Page\n");
        printf("5. Show Full History\n");
        printf("6. Exit\n");
        printf("Enter choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: visitPage(); break;
            case 2: goBack(); break;
            case 3: goForward(); break;
            case 4: showCurrent(); break;
            case 5: showHistory(); break;
            case 6:
                freeAll();
                printf("Exiting... History cleared.\n");
                break;
            default:
                printf("Invalid option!\n");
        }
    } while (choice != 6);

    return 0;
}
