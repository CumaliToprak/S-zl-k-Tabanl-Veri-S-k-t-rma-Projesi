#include <stdio.h>
#include <stdint.h>
#include <string.h>
#include <stdlib.h>
#include <dirent.h>
#define MAX_TREE_HT 100
#define maxLine 150
#define OFFSETBITS 6
#define LENGTHBITS (8-OFFSETBITS)
#define OFFSETMASK ((1 << (OFFSETBITS)) - 1)
#define LENGTHMASK ((1 << (LENGTHBITS)) - 1)
#define GETOFFSET(x) (x >> LENGTHBITS)
#define GETLENGTH(x) (x & LENGTHMASK)
#define OFFSETLENGTH(x,y) (x << LENGTHBITS | y)


int array[256]= {0};

struct MinHeapNode
{
    char data;
    unsigned freq;
    struct MinHeapNode *left, *right;
};

struct MinHeap
{

    unsigned size;
    unsigned capacity;
    struct MinHeapNode** array;
};


struct MinHeapNode* newNode(char data, unsigned freq)
{
    struct MinHeapNode* temp
        = (struct MinHeapNode*)malloc
          (sizeof(struct MinHeapNode));

    temp->left = temp->right = NULL;
    temp->data = data;
    temp->freq = freq;

    return temp;
}

struct MinHeap* createMinHeap(unsigned capacity)

{

    struct MinHeap* minHeap
        = (struct MinHeap*)malloc(sizeof(struct MinHeap));

    minHeap->size = 0;
    minHeap->capacity = capacity;
    minHeap->array
        = (struct MinHeapNode**)malloc(minHeap->
                                       capacity * sizeof(struct MinHeapNode*));
    return minHeap;
}

void swapMinHeapNode(struct MinHeapNode** a,
                     struct MinHeapNode** b)

{

    struct MinHeapNode* t = *a;
    *a = *b;
    *b = t;
}

void minHeapify(struct MinHeap* minHeap, int idx)

{

    int smallest = idx;
    int left = 2 * idx + 1;
    int right = 2 * idx + 2;

    if (left < minHeap->size && minHeap->array[left]->
            freq < minHeap->array[smallest]->freq)
        smallest = left;

    if (right < minHeap->size && minHeap->array[right]->
            freq < minHeap->array[smallest]->freq)
        smallest = right;

    if (smallest != idx)
    {
        swapMinHeapNode(&minHeap->array[smallest],
                        &minHeap->array[idx]);
        minHeapify(minHeap, smallest);
    }
}

int isSizeOne(struct MinHeap* minHeap)
{

    return (minHeap->size == 1);
}

struct MinHeapNode* extractMin(struct MinHeap* minHeap)

{

    struct MinHeapNode* temp = minHeap->array[0];
    minHeap->array[0]
        = minHeap->array[minHeap->size - 1];

    --minHeap->size;
    minHeapify(minHeap, 0);

    return temp;
}

void insertMinHeap(struct MinHeap* minHeap,
                   struct MinHeapNode* minHeapNode)

{

    ++minHeap->size;
    int i = minHeap->size - 1;

    while (i && minHeapNode->freq < minHeap->array[(i - 1) / 2]->freq)
    {

        minHeap->array[i] = minHeap->array[(i - 1) / 2];
        i = (i - 1) / 2;
    }

    minHeap->array[i] = minHeapNode;
}

void buildMinHeap(struct MinHeap* minHeap)

{

    int n = minHeap->size - 1;
    int i;

    for (i = (n - 1) / 2; i >= 0; --i)
        minHeapify(minHeap, i);
}


void printArr(int arr[], int n)
{
    int i;
    for (i = 0; i < n; ++i)
    {
        printf("%d", arr[i]);
    }

    printf("\n");
}

int isLeaf(struct MinHeapNode* root)

{
    return !(root->left) && !(root->right);
}

struct MinHeap* createAndBuildMinHeap(char data[], int freq[], int size)

{

    struct MinHeap* minHeap = createMinHeap (size);

    for (int i = 0; i < size; ++i)
        minHeap->array[i] = newNode(data[i], freq[i]);

    minHeap->size = size;
    buildMinHeap(minHeap);

    return minHeap;
}

struct MinHeapNode* buildHuffmanTree(char data[], int freq[], int size)

{
    struct MinHeapNode *left, *right, *top;

    struct MinHeap* minHeap = createAndBuildMinHeap(data, freq, size);

    while (!isSizeOne(minHeap))
    {

        left = extractMin(minHeap);
        right = extractMin(minHeap);

        top = newNode('$', left->freq + right->freq);

        top->left = left;
        top->right = right;

        insertMinHeap(minHeap, top);
    }

    return extractMin(minHeap);
}

char charArr[256]= {};
int flag=0;
void printCodes(struct MinHeapNode* root, int arr[], int top)

{
    if (root->left)
    {

        arr[top] = 0;
        printCodes(root->left, arr, top + 1);
    }
    if (root->right)
    {

        arr[top] = 1;
        printCodes(root->right, arr, top + 1);
    }

    if (isLeaf(root))
    {
        charArr[flag]=root->data;
        array[flag]=top;
        flag++;

        printf("%c : ",root->data);
        printArr(arr, top);
    }
}

void HuffmanCodes(char data[], int freq[], int size)
{
    struct MinHeapNode* root
        = buildHuffmanTree(data, freq, size);

    int arr[MAX_TREE_HT], top = 0;

    printCodes(root, arr, top);
}

struct isaret
{
    uint8_t offset_len;
    char c;
};

int kac_karakter_eslesdi(char *s1, char *s2, int sinir)
{
    int len = 0;
    while (*s1++ == *s2++ && len < sinir)
        len++;
    return len;
}

void memcopy_lz77(char *s1, char *s2, int boyut)
{
    while (boyut--)
        *s1++ = *s2++;
}

char *cozum(struct isaret *isaretler, int isaret_num, int *cozulu_pcb)
{
    int kapasite = 1 << 3;
    *cozulu_pcb = 0;
    char *cozulu = malloc(sizeof(kapasite));
    int x;
    for (x = 0; x < isaret_num; x++)
    {
        int offset = GETOFFSET(isaretler[x].offset_len);
        int len = GETLENGTH(isaretler[x].offset_len);
        char c = isaretler[x].c;
        if (*cozulu_pcb + len + 1 > kapasite)
        {
            kapasite = kapasite << 1;
            cozulu = realloc(cozulu, kapasite);
        }
        if (len > 0)
        {
            memcopy_lz77(&cozulu[*cozulu_pcb], &cozulu[*cozulu_pcb - offset], len);
        }
        *cozulu_pcb += len;

        cozulu[*cozulu_pcb] = c;

        *cozulu_pcb = *cozulu_pcb + 1;
    }
    return cozulu;
}

struct isaret *kodlama(char *yazi, int sinir, int *isaret_num)
{
    int kapasite = 1 << 3;

    int _isaret_num = 0;

    struct isaret i;

    char *ileritampon, *aramatampon;

    struct isaret *kodlu = malloc(kapasite * sizeof(struct isaret));

    for (ileritampon = yazi; ileritampon < yazi + sinir; ileritampon++)
    {
        aramatampon = ileritampon - OFFSETMASK;

        if (aramatampon < yazi)
            aramatampon = yazi;

        int maks_len = 0;

        char *maks_match = ileritampon;

        for (; aramatampon < ileritampon; aramatampon++)
        {
            int len = kac_karakter_eslesdi(aramatampon, ileritampon, LENGTHMASK);

            if (len > maks_len)
            {
                maks_len = len ;
                maks_match = aramatampon ;
            }
        }


        if (ileritampon + maks_len >= yazi + sinir)
        {
            maks_len = yazi + sinir - ileritampon - 1;
        }

        i.offset_len = OFFSETLENGTH((ileritampon - maks_match), (maks_len));

        ileritampon += maks_len;
        i.c = *ileritampon;


        if (_isaret_num + 1 > kapasite)
        {
            kapasite = kapasite << 1;
            kodlu = realloc(kodlu, kapasite * sizeof(struct isaret));
        }

        kodlu[_isaret_num++] = i;
    }

    if (isaret_num)
        *isaret_num = _isaret_num;

    return kodlu;
}

char *dosya_oku(FILE *f, int *boyut)
{
    char *icerik;
    fseek(f, 0, SEEK_END);
    *boyut = ftell(f);
    icerik = malloc(*boyut);
    fseek(f, 0, SEEK_SET);
    fread(icerik, 1, *boyut, f);
    return icerik;
}

int main()
{
    FILE *f;
    char usedChar[256]= {};
    int charFrequency[256]= {0};
    int i=0, flag=0;
    int metin_boyutu = 8, isaret_sayisi, cozum_boyutu;
    char *kaynak_metin = "", *cozulu_metin = "";
    struct isaret *kodlu_metin;
    int temp;
    int  j;
    char chrTemp;


    if ((f = fopen("metin.txt", "rb"))!=NULL)
    {
        kaynak_metin= dosya_oku(f, &metin_boyutu);
        fclose(f);
    }
    else
    {
        printf("metin.txt dosyasi acilamadi kontrol ediniz!!!");
    }


    for(i=0; i<metin_boyutu; i++)
    {
        if(charFrequency[(int)kaynak_metin[i]]==0)
        {
            usedChar[(int)kaynak_metin[i]]=kaynak_metin[i];
            flag++;
        }
        charFrequency[(int)kaynak_metin[i]]+=1;
    }
    char usedChar2[flag];
    int charFrequency2[flag];
    int counter = 0;
    for(i=0; i<256; i++)
    {
        if(charFrequency[i]!=0)
        {
            usedChar2[counter]=usedChar[i];
            charFrequency2[counter]=charFrequency[i];
            counter++;
        }
    }

    for (i=1; i<flag; i++)
    {
        for (j=0; j<flag-i; j++)
        {
            if(charFrequency2[j] > charFrequency2[j+1])
            {
                temp = charFrequency2 [j];
                charFrequency2 [j] = charFrequency2 [j+1];
                charFrequency2 [j+1] = temp;

                chrTemp = usedChar2[j];
                usedChar2[j] = usedChar2[j+1];
                usedChar2[j+1] = chrTemp;

            }
        }
    }
    printf("HUFFMAN SONUCU\n****************\n");
    HuffmanCodes(usedChar2, charFrequency2, flag);

    printf("\n\n\n");
    int compressedAmount = 0;
    int uncompressedAmount = 0;
    for(i=0; i<flag; i++)
    {
        for(j=0; j<flag; j++)
        {
            if(usedChar2[i]==charArr[j])
            {
                compressedAmount+=array[i]*charFrequency2[j];
                uncompressedAmount+=8*charFrequency2[j];
                break;
            }
        }

    }
    printf("NORMAL BOYUTU: %d byte, SIKISTIRILMIS BOYUTU: %d byte\n\n\n\n",  uncompressedAmount/8, compressedAmount/8);


    kodlu_metin = kodlama(kaynak_metin, metin_boyutu, &isaret_sayisi);

    if ((f = fopen("kodlu.txt", "wb"))!=NULL)
    {
        fwrite(kodlu_metin, sizeof(struct isaret), isaret_sayisi, f);
        fclose(f);
    }

    cozulu_metin = cozum(kodlu_metin, isaret_sayisi, &cozum_boyutu);

    if ((f = fopen("cozulu.txt", "w"))!=NULL)
    {
        fwrite(cozulu_metin, 1, cozum_boyutu, f);
        fclose(f);
    }

    printf("\nLZ77 SONUCU\n****************\nNORMAL BOYUTU: %d byte, SIKISTIRILMIS BOYUTU: %lu byte, COZUM BOYUTU: %d byte", metin_boyutu, isaret_sayisi * sizeof(struct isaret), cozum_boyutu);

    return 0;
}
