# Sort
#include <stdio.h>
#include <errno.h>
#include <stdlib.h>
#include <ctype.h>

#define CHECKERROR(X, Y);  \
if (!X)                    \
    {                      \
    perror(Y);             \
    return LOSE;           \
    }

const int LOSE = 0;
const int WIN = 1;
const char* READFILENAME =             "/home/alex/Desktop/Shekspir.txt";
const char* WRITESAVEFILENAME =        "/home/alex/Desktop/test.txt";
const char* WRITEALPHABETFILENAME =    "/home/alex/Desktop/inalpha.txt";
const char* WRITEENDALPHABETFILENAME = "/home/alex/Desktop/unalpha.txt";

struct String
        {
        char* str;
        int len;
        };
//=========================================================================================
FILE* FileOpen (const char* FileName, const char* mode)
    {
    FILE* fp = fopen (FileName, mode);
    if (!fp)
        {
        errno = ENOENT;
        return NULL;
        }
    return fp;
    }
//=========================================================================================
int FileLen (FILE* file)
	{
    fseek (file, 0, SEEK_END);
	int len = ftell (file);
	rewind (file);
	if (!len)
		{
		errno = EPERM;
		return 0;
		}
	return len;
	}
//=========================================================================================
char* InputDinamic (const int Size)
    {
    if (Size <= 0)
        {
        errno = EDOM;
        return NULL;
        }

    char* arr = (char*) calloc (Size, sizeof (*arr));
    if (!arr)
        {
        errno = ENOMEM;
        return NULL;
        }
    return arr;
    }
//=========================================================================================
int FindCountStr (char* buffer, int len)
    {
    if ((!buffer) || (!len))
        {
        errno = EDOM;
        return 0;
        }
    int CountStr = 0;
    for (int i = 0; i < len; i++)
        {
        if (buffer[i] == '\n')
            CountStr++;
        }
    return CountStr;
    }
//=========================================================================================
String* InputDinamicString (const int Size)
    {
    if (Size <= 0)
        {
        errno = EDOM;
        return NULL;
        }

    struct String* arr = (String*) calloc (Size, sizeof (*arr));
    if (!arr)
        {
        errno = ENOMEM;
        return NULL;
        }
    return arr;
    }
//=========================================================================================
bool FillStr (char* Buffer,const int len, String* Str, const int CountStr)
    {
    if ((CountStr <= 0) || (!Buffer) || (!Str) || (!len))
        {
        errno = EDOM;
        return false;
        }

    int NumberStr = 0;

    for (int i = 0; i < len; i++)
        {
        if (i == 0)
            {
            (Str[NumberStr]).str = Buffer;
            NumberStr++;
            }
        if (Buffer[i] == '\n')
            {
            Buffer[i] = '\0';
            (Str[NumberStr]).str = Buffer + i + 1;
            (Str[NumberStr - 1]).len = (Str[NumberStr]).str - (Str[NumberStr - 1]).str;
            NumberStr++;
            }
        if (NumberStr == CountStr)
            {
            (Str[NumberStr - 1]).len = Buffer + len - (Str[NumberStr - 1]).str;
            break;
            }
        }
    return true;
    }
//=========================================================================================
char*  StringArrToBuf (String* Text,const int CountStr, int *Size)
    {
    for (int i = 0; i < CountStr; i++)
        if ((Text[i]).len > 1)
            *Size += (Text[i]).len;
    char* arr = (char*) calloc (*Size, sizeof(*arr));
    int Num = 0;
    for (int i = 0; i < CountStr; i++)
        if ((Text[i]).len > 1)
        for (int j = 0; j < Text[i].len; j++)
        {
            arr[Num] = Text[i].str[j];
            Num++;
        }
    return arr;
    }
//=========================================================================================
bool FileWrite (FILE* File, String* Text, const int CountStr)
    {
    if ((!Text) || (!CountStr))
        {
        errno = EDOM;
        return false;
        }
    int BufLength = 0;
    char* Buf = StringArrToBuf(Text, CountStr, &BufLength);
    for (int i = 0; i < BufLength - 1; i++)
        {
        if (Buf[i] == '\0')
            Buf[i] = '\n';
        fprintf(File, "%c", Buf[i]);
        }

    return true;
    }
//=========================================================================================
bool LeadTextToStd (String* Text, const int CountStr)
    {
    if ((!Text) || (!CountStr))
        {
        errno = EDOM;
        return false;
        }
    for (int i = 0; i < CountStr; i++)
        {
        while (isspace ((Text[i]).str[0]) && ((Text[i]).len > 1))
            {
            (Text[i]).str = &(Text[i]).str[1];
            (Text[i]).len--;
            }
        while (((Text[i]).len > 1) && (isspace ((Text[i]).str[(Text[i]).len - 2])))
            {
            (Text[i]).str[(Text[i]).len - 2] = '\0';
            (Text[i]).len--;
            }
        }
    return true;
    }
//=========================================================================================
int FindStartAlpha (String Text)
    {
    int Start = -1;
    if (Text.len <= 1)
        return Start;

    while (Start < Text.len - 1)
        {
        Start++;
        if (isalpha (Text.str[Start]))
            return (Start);
        }
    return -1;
    }
//=========================================================================================
int Mystrcmp (String Text1, String Text2)
    {
    int Start1 = FindStartAlpha (Text1),
        Start2 = FindStartAlpha (Text2);
    if ((Start1 < 0) && (Start2 < 0))
        return 0;
    if (Start1 < 0)
        return -1;
    if (Start2 < 0)
        return 1;
    int i = 0;
    for (i = 0; tolower (Text1.str[Start1 + i]) == tolower (Text2.str[Start2 + i]); i++)
        if (Text1.str[Start1 + i] == '\0')
            return 0;
    return (tolower (Text1.str[Start1 + i]) - tolower (Text2.str[Start2 + i]));
    }
//=========================================================================================
int FindEndAlpha (String Text)
    {
    if (Text.len <= 1)
        return -1;

    int End = Text.len;
    while (End > 0)
        {
        End--;
        if (isalpha (Text.str[End]))
            return (End);
        }
    return -1;
    }
//=========================================================================================
int Mystrrewcmp (String Text1, String Text2)
    {
    int End1 = FindEndAlpha (Text1),
        End2 = FindEndAlpha (Text2);
    if ((End1 < 0) && (End2 < 0))
        return 0;
    if (End1 < 0)
        return -1;
    if (End2 < 0)
        return 1;
    int i = 0;
    for (i = 0; tolower (Text1.str[End1 - i]) == tolower (Text2.str[End2 - i]); i++)
        if (Text1.str[End1 - i] == '\0')
            return 0;
    return (tolower (Text1.str[End1 - i]) - tolower (Text2.str[End2 - i]));
    }
//=========================================================================================
void Swap (String* s1, String* s2)
    {
    String Save = *s1;
    *s1 = *s2;
    *s2 = Save;
    }
//=========================================================================================
bool Sort (String* Text, const int CountStr, int (*compare)(String Text1, String Text2))
    {
    if ((!Text) || (!CountStr))
        {
        errno = EDOM;
        return false;
        }
    for (int i = 0; i < CountStr; i++)
        {
        for (int j = 0; j < CountStr - i - 1; j++)
            {
            if (((Text[j]).len <= 1) || ((*compare)(Text[j], Text[j + 1]) > 0))
                Swap (&Text[j], &Text[j + 1]);
            }
        }
    return true;
    }
//=========================================================================================
bool String_destruct (String* This)
    {
    if (!This)
        {
        errno = EDOM;
        return false;
        }
    (*This).str = NULL;
    (*This).len = -1;
    free ((*This).str);
    return true;
    }
//=========================================================================================
bool DeliteArrString (String* Arr, const int Size)
    {
    if ((!Arr) || (!Size))
        {
        errno = EDOM;
        return false;
        }
    for (int i = 0; i < Size; i++)
        if (!String_destruct(&Arr[i]))
        {
        errno = EDOM;
        return false;
        }
    free (Arr);
    return true;
    }
//=========================================================================================
bool DeliteBuf (char* buffer)
    {
    if (!buffer)
        {
        errno = EDOM;
        return false;
        }
    free (buffer);
    buffer = NULL;
    return true;
    }
//=========================================================================================
bool String_OK (const String* This)
    {
    return (!(This) || !((*This).str) || !(*This).len > 0);
    }
//=========================================================================================
void String_Dump (const String* This)
    {
    if (String_OK(This))
        printf("Struct (Bad) \n"
        "{This structure is flawed}");
    else
        {
        printf("Struct (OK) \n"
        "{\nstr = <");
        int MaxLine = 80;
        if ((*This).len < MaxLine)
            MaxLine = (*This).len;
        for (int i = 0; i < MaxLine; i++)
            printf("%c", (*This).str[i]);
        printf("> \n");
        if ((*This).len > MaxLine)
            {
            printf("Not all the characters are derived.\n");
            if ((*This).str[MaxLine] != '\0')
                printf("The string is not over.\n");
            }
        printf("len = %d", (*This).len);
        }
    }
//=========================================================================================
//=========================================================================================
int main()
	{
    printf ("# \"Sort text\" v.1.0.0 by Al.Liogky \n");

    FILE* fp = FileOpen (READFILENAME, "rb");
    CHECKERROR (fp, "Error open file");

    int len = FileLen (fp);
    CHECKERROR (len, "Error file size");
    char* buffer = InputDinamic (len);
    CHECKERROR (buffer, "Error initialization buffer");

    fread (buffer, len, sizeof (*buffer), fp);
    if (ferror (fp))
        {
        errno = EILSEQ;
        perror ("Error reading file");
        return LOSE;
        }
    fclose (fp);

    int CountStr = FindCountStr (buffer, len);

    struct String* text = InputDinamicString (CountStr);
    CHECKERROR (text, "Error initialization struct of pointers")
    CHECKERROR (FillStr (buffer, len, text, CountStr), "Error data");

    FILE* fs = FileOpen (WRITESAVEFILENAME, "wb");
    CHECKERROR (fs, "Error open file");
    CHECKERROR (FileWrite (fs, text, CountStr), "Error writinging file");
    fclose (fs);

    CHECKERROR (LeadTextToStd (text, CountStr), "Error align the text to the standard form")

    FILE* fa = FileOpen (WRITEALPHABETFILENAME, "wb");
    CHECKERROR (fa, "Error open file");
    CHECKERROR (Sort (text, CountStr, &Mystrcmp), "Error data");
    CHECKERROR (FileWrite (fa, text, CountStr), "Error writinging file");
    fclose (fa);

    FILE* fea = FileOpen (WRITEENDALPHABETFILENAME , "wb");
    CHECKERROR (fea, "Error open file");
    CHECKERROR (Sort (text, CountStr, &Mystrrewcmp), "Error data");
    CHECKERROR (FileWrite (fea, text, CountStr), "Error writinging file");
    fclose (fea);

    if ((!DeliteArrString(text, CountStr)) || (!DeliteBuf(buffer)))
        perror("Warning. Failed to clean up used memory");

    return 1;
    }
