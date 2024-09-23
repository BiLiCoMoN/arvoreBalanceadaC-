# Arvore Balanceada C++

#include <iostream>
using namespace std;

// Estrutura do nó da árvore
struct Node {
    int key;
    Node* left;
    Node* right;
    int height;
    
    Node(int value) : key(value), left(nullptr), right(nullptr), height(1) {}
};

// Função para obter a altura de um nó
int getHeight(Node* node) {
    return node ? node->height : 0;
}

// Função para calcular o fator de balanceamento
int getBalance(Node* node) {
    return node ? getHeight(node->left) - getHeight(node->right) : 0;
}

// Função para realizar a rotação à direita
Node* rightRotate(Node* y) {
    Node* x = y->left;
    Node* T2 = x->right;
    
    x->right = y;
    y->left = T2;

    // Atualiza as alturas
    y->height = max(getHeight(y->left), getHeight(y->right)) + 1;
    x->height = max(getHeight(x->left), getHeight(x->right)) + 1;

    return x;
}

// Função para realizar a rotação à esquerda
Node* leftRotate(Node* x) {
    Node* y = x->right;
    Node* T2 = y->left;

    y->left = x;
    x->right = T2;

    // Atualiza as alturas
    x->height = max(getHeight(x->left), getHeight(x->right)) + 1;
    y->height = max(getHeight(y->left), getHeight(y->right)) + 1;

    return y;
}

// Função para inserir um novo nó na árvore AVL
Node* insert(Node* node, int key) {
    // 1. Realiza a inserção normal na árvore binária
    if (!node) return new Node(key);

    if (key < node->key)
        node->left = insert(node->left, key);
    else if (key > node->key)
        node->right = insert(node->right, key);
    else
        return node; // Chaves duplicadas não são permitidas

    // 2. Atualiza a altura do nó ancestral
    node->height = 1 + max(getHeight(node->left), getHeight(node->right)));

    // 3. Verifica o balanceamento do nó e realiza as rotações necessárias
    int balance = getBalance(node);

    // Caso 1: desbalanceamento à esquerda
    if (balance > 1 && key < node->left->key)
        return rightRotate(node);

    // Caso 2: desbalanceamento à direita
    if (balance < -1 && key > node->right->key)
        return leftRotate(node);

    // Caso 3: desbalanceamento à esquerda direita
    if (balance > 1 && key > node->left->key) {
        node->left = leftRotate(node->left);
        return rightRotate(node);
    }

    // Caso 4: desbalanceamento à direita esquerda
    if (balance < -1 && key < node->right->key) {
        node->right = rightRotate(node->right);
        return leftRotate(node);
    }

    return node; // Retorna o ponteiro do nó (sem alterações)
}

// Função para buscar um valor na árvore
Node* search(Node* root, int key) {
    if (!root || root->key == key)
        return root;

    if (key < root->key)
        return search(root->left, key);
    
    return search(root->right, key);
}

// Função para imprimir a árvore em ordem (in-order)
void inOrder(Node* root) {
    if (root) {
        inOrder(root->left);
        cout << root->key << " ";
        inOrder(root->right);
    }
}

// Função principal
int main() {
    Node* root = nullptr;

    // Inserindo valores na árvore
    int keys[] = {10, 20, 30, 40, 50, 25};
    for (int key : keys) {
        root = insert(root, key);
    }

    // Imprimindo a árvore em ordem
    cout << "Árvore em ordem: ";
    inOrder(root);
    cout << endl;

    // Buscando um valor
    int searchKey = 30;
    Node* result = search(root, searchKey);
    if (result) {
        cout << "Valor " << result->key << " encontrado!" << endl;
    } else {
        cout << "Valor não encontrado." << endl;
    }

    return 0;
}
