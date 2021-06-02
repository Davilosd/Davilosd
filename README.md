#include <iostream>
#include <conio.h>
#include <stdio.h>
#include <string>
#include <fstream>
#include <Windows.h>
#include <ctime>
using namespace std;

const int MAXLISTLH = 500;
const int MAXLISTMH = 200;
const int MAXLIST = 10000;
const int UP = 72;
const int DOWN = 80;
const int LEFT = 75;
const int RIGHT = 77;
const int ENTER = 13;
const int ESC = 27;
const int BACKSPACE = 8;
const int TAB = 9;
const int F1 = 17;
const int F5 = 63;
const int ArrowUP = 25;
const int ArrowDOWN = 24;
const int INS = 82;
const int DEL = 83; 
const int HOME = 71;
const int END = 79;
const int PGU = 73;
const int PGD = 81;

#define boxx 18 //Vi tri x bat dau cua box
#define boxy 15 //Vi tri y bat dau cua box
#define boxs 60 //Box size
#define tabx 6 // vi tri x bat dau cua table
#define taby 4	// vi tri y bat dau cua table
#define tabs 83 // table box 
#define tabh 23 // table hight
#define color_text1 15 //mau chu 1
#define color_text2 15 //mau chu 2
#define color_fill1 46 // mau nen 1
#define color_fill2 46 // mau nen 2

void TableMon();
void TableDiem(int);
void TableLop();
void TableSv();
void Menu_Button(int ,int);
void Raw_Button_F1(int, int);
void Raw_Button_F5(int , int );
void Raw_Button_DEL(int , int);
void Raw_Button_ESC(int , int);
void Raw_Button_TAB(int , int);
void Raw_Button_OK(int , int );
void Raw_Button_YES(int);
void Raw_Button_NO(int);
void Frame_Lop(int , int );
void Box_XacnhanLuu();
void Box_XacnhanXoa();
bool XacNhanLuuFile();
bool XacNhanNhapLai();

void gotoxy(int x, int y)
{
	HANDLE hConsoleOutput;
	COORD Cursor_an_Pos = { x - 1, y - 1 };
	hConsoleOutput = GetStdHandle(STD_OUTPUT_HANDLE);
	SetConsoleCursorPosition(hConsoleOutput, Cursor_an_Pos);
}
void textcolor(int x)
{
	HANDLE mau;
	mau = GetStdHandle(STD_OUTPUT_HANDLE);
	SetConsoleTextAttribute(mau, x);
}
void resizeConsole(int width, int height)
{
	HWND console = GetConsoleWindow();
	RECT r;
	GetWindowRect(console, &r);
	MoveWindow(console, r.left, r.top, width, height, TRUE);
}

void SetWindowSize(SHORT width, SHORT height)
{
    HANDLE hStdout = GetStdHandle(STD_OUTPUT_HANDLE);
    SMALL_RECT WindowSize;
    WindowSize.Top = 0;
    WindowSize.Left = 0;
    WindowSize.Bottom = height-1;
    WindowSize.Right = width-1;

    SetConsoleWindowInfo(hStdout, 1, &WindowSize);
}

void SetScreenBufferSize(SHORT width, SHORT height)
{
    HANDLE hStdout = GetStdHandle(STD_OUTPUT_HANDLE);

    COORD NewSize;
    NewSize.X = width;
    NewSize.Y = height;

    SetConsoleScreenBufferSize(hStdout, NewSize);
}

typedef struct MonHoc
{
	char MaMh[11], TenMh[31], LT[3], TH[3];
	int STT;
} Monhoc;

struct NodeMonHoc
{    
	 Monhoc monhoc;
	 NodeMonHoc *left;
	 NodeMonHoc *right;
	
};
typedef  NodeMonHoc *ListMonHoc;

// danh sach diem
struct Diem
{
	char MaMh[11];
	char diem[5];
	char Lan[2];
};

struct NodeDiem
{
	Diem data;
	struct NodeDiem *next;
};
typedef struct NodeDiem *ListDiemPtr;

// danh sach sv
struct SinhVien
{
	char MaSv[15], Ho[31], Ten[11], Phai[4], SoDt[12],MaLop[15];
	ListDiemPtr First;
};

struct NODESV
{
	SinhVien info;
	struct NODESV *next;
};
typedef struct NODESV NodeSv;
typedef struct NODESV *ListSvPtr;

struct LISTSV{
	NodeSv *head;
	NodeSv *tail;
};
typedef struct LISTSV ListSv;

void KhoiTao(ListSv &l){
	l.head = NULL;
	l.tail = NULL;
}
NodeSv *KhoiTaoNode(SinhVien x){
	NodeSv *p = new NodeSv;
	if (p == NULL) return NULL;
	p->info = x;
	p->next = NULL;
	return p;
}
void ThemVaoCuoi(ListSv &l, NodeSv *p){
	if(l.head==NULL)   l.head = l.tail = p;
	else {
		l.tail->next = p;
		l.tail = p;
	}
}

// ds lop
struct LopTinChi
{
	char MaLop[11], NienKhoa[5],HocKy[3],Nhom[4],SvMin[3],SvMax[4],MaMh[11];
	int MaLopTC;
	bool HuyLop;
	ListSvPtr First;
};

struct ListLopTinChi
{
	int n;
	LopTinChi LLTC[MAXLISTLH];
};

int Search_info(ListLopTinChi plist, char info[])
{
	for ( int i =0 ; i <plist.n ; i++)
  	     if (strcmp(plist.LLTC[i].MaLop, info)==0) return i;
	return -1;
}

void Delete_LH (struct ListLopTinChi &plist, char s[])
{
	if(Search_info(plist,s)==-1)   return;
	for(int j = Search_info(plist,s)+1;  j< plist.n ; j++)
		plist.LLTC[j-1] = plist.LLTC[j];
	plist.n--;
	return;   
}

void Delete_VT (struct ListLopTinChi &plist, int a)
{
	for(int j = a;  j< plist.n ; j++)
		plist.LLTC[j] = plist.LLTC[j+1];
	plist.n--;
	return;   
}

int Random(int res){
	srand(time(NULL)); 
	res = rand() % (1000-1 +1) + 1;
	return res;
}

// XU li diem
void ThemDiemVaoDau(ListDiemPtr &First, Diem d)
{
	ListDiemPtr p = new NodeDiem;
	p->data = d;
	p->next = First;
	First = p;
}

int XoaDiemDau(ListDiemPtr &First)
{
	if ( First == NULL )
		return 0;
	ListDiemPtr p = First;
	First = p->next;
	delete p;
	return 1;
}

int DemSoLuongDiem(ListDiemPtr First)
{
	int dem = 0;
	for(ListDiemPtr q = First;q != NULL;q = q->next)
	{
		dem++;
	}
	return dem;
}

void XoaDiemTheoViTri(ListDiemPtr &First, Diem d, int vitri) //Xoa Node k trong danh sach
{
    ListDiemPtr q,p = First;
    int i=1;
    if (vitri < 1 || vitri > DemSoLuongDiem(First)) return;
    else
    {
        if (vitri == 1) XoaDiemDau(First); //xoa vi tri dau tien
        else //xoa vi tri k != 1
        {
            while (p != NULL && i != vitri-1) //duyet den vi tri k-1
            {
                p=p->next;
                i++;
            }
            q = p->next;
            p->next = q->next;
            delete q;
        }
    }
}

int DuyetDanhSachDiem(ListDiemPtr First, Diem d) 
{
    int i=1;
    
    for(ListDiemPtr p = First; p!=NULL; p = p->next,i++)
    {
    	if(strcmp(p->data.MaMh,d.MaMh) == 0 && strcmp(p->data.Lan,d.Lan) == 0 )
    	{
			return i;
		}   	
    }
    return -1; //khong tim thay
}

void LocDiem(ListDiemPtr &First)
{
	if(First == NULL)
		return;
	
	// xoa diem rong
	for(ListDiemPtr u = First; u != NULL; u = u->next)
	{
		if(strcmp(u->data.diem,"") == 0)
		{
			XoaDiemTheoViTri(First, u->data, DuyetDanhSachDiem(First,u->data));
		}		
	}
	
	for(ListDiemPtr p = First; p->next != NULL; p = p->next)
	{
		for(ListDiemPtr q = p->next; q != NULL; q = q->next)
		{
			if(strcmp(p->data.MaMh,q->data.MaMh) == 0)
			{
				if(atof(p->data.diem) >= atof(q->data.diem))
				{
					XoaDiemTheoViTri(First, q->data, DuyetDanhSachDiem(First,q->data));
				}
				else
				{
					XoaDiemTheoViTri(First, p->data, DuyetDanhSachDiem(First,p->data));
				}			
			}	
		}
	}
}

string TruyXuatDiem(ListDiemPtr First, char mamh[], string lan)
{
	string temp;
	for(ListDiemPtr p = First; p!=NULL; p = p->next)
    {
    	if(strcmp(p->data.MaMh,mamh) == 0 && strcmp(p->data.Lan,lan.c_str()) == 0)
    	{
			temp = p->data.diem;
			return temp;
		}   	
    }
}

void XoaDiem(ListSvPtr &First, Diem d)
{
	for(ListSvPtr p = First; p !=NULL; p = p->next)
	{	
		XoaDiemTheoViTri(p->info.First, d, DuyetDanhSachDiem(p->info.First,d));
	}
}

// Xu li sinh vien


void ThemSvDau(ListSvPtr &First, SinhVien sv)
{
	ListSvPtr p = new NodeSv;
	p->info = sv;
	p->next = First;
	First = p;
}

void ThemSvVaoSau(ListSv &l, SinhVien sv)
{
    ListSvPtr p = new NodeSv;
    p->info = sv;
    p->next = NULL;
    if (l.head == NULL)
        l.head = p;
    else
    {
        for (l.tail = l.head; l.tail->next != NULL; l.tail = l.tail->next);
        l.tail->next = p;
    }
}

int XoaSvDau(ListSvPtr &First)
{
	if ( First == NULL )
		return 0;
	ListSvPtr p = First;
	First = p->next;
	delete p;
	return 1;
}

int TongSoSinhVien(ListSv &l)
{
	int dem = 0;
	for (NodeSv *p = l.head; p != NULL; p = p->next)
	{
		dem++;
	}
	return dem;
}

void SapXepSv(ListSv &l)
{
    ListSvPtr temp ,p ,q;
    SinhVien sv ;
	for (p = l.head; p->next!= NULL; p = p->next)
	{
		temp = p;
		sv = p->info ;
		for (q = p->next; q != NULL; q =q->next)
		{
			if(strcmp(temp ->info.Ten, q->info.Ten) == 1) // temp.ten > q.ten
				temp = q;
			else
			{
				if(strcmp(temp ->info.Ten, q ->info.Ten) == 0)
		    	{
					if(strcmp(temp->info.Ho, q->info.Ho) == 1)
						temp = q ;
		     	}
		    }
		}
		p->info = temp->info ;
	    temp->info = sv;
	}
}



ListSvPtr CheckMaSv_Truong(ListLopTinChi llh, char macheck[])
{	
	for (int i = 0 ; i < llh.n ; i++)
	{		
		for ( ListSvPtr p = llh.LLTC[i].First ; p!= NULL ; p = p -> next)
		{
			if ( strcmp( p ->info.MaSv, macheck ) == 0)
				return p ;
		}
	}
	return NULL;
}

ListSvPtr CheckMaSv_Lop(ListSvPtr First, char macheck[])
{	
	for (ListSvPtr p= First; p!= NULL; p = p ->next)
	{
		if(strcmp( p ->info.MaSv, macheck) == 0)
			return p ;
	}
	return NULL;
}

// xu li lop
NodeSv *CheckMaLop(ListSv l, char temp[], NodeSv* &p)
{
	for(p=l.head; p!=NULL;p=p->next){
			if(strcmp(p->info.MaLop, temp)==0){
			return p;
	}
}
	return NULL;
}

void SapXepLopHoc(ListLopTinChi &llh)
{
    LopTinChi temp;
	for (int i = 0;i < llh.n - 1;i++)
	{
		for (int j = i+1; j < llh.n; j++)
		{
			//if(strcmp(llh.LLTC[i].TenLop,llh.LLTC[j].TenLop) == 1){
			if(llh.LLTC[i].MaLopTC>llh.LLTC[j].MaLopTC){
				temp = llh.LLTC[i];
				llh.LLTC[i] = llh.LLTC[j];
				llh.LLTC[j] = temp;
			}
		}
	}
}

int CheckMaMh(ListMonHoc lmh, char macheck[])
{
	
	while(lmh!=NULL)
	{if( strcmp(lmh->monhoc.MaMh,macheck)==0)
	 return 1;
	else if( strcmp(lmh->monhoc.MaMh,macheck)==1)
	 lmh=lmh->left;
    else lmh=lmh->right;
	}
	return -1;
}

// xu li mon hoc


void InsertMonHoc(ListMonHoc &lmh, Monhoc a)
{
    	if(lmh==NULL)
	{ lmh=new NodeMonHoc;
	lmh->monhoc=a;
	lmh->left= NULL;
	lmh->right=NULL;
    
	} 
	else if(strcmp(a.TenMh,lmh->monhoc.TenMh)==1) InsertMonHoc(lmh->right,a);
	else if(strcmp(a.TenMh,lmh->monhoc.TenMh)==-1) InsertMonHoc(lmh->left,a);
	
}
/*	DATA FILE MON HOC */
void SaveData_Mh(MonHoc monhoc)
{
		fstream file;
	file.open("DSMH.DAT",ios::app);
    file<<monhoc.LT<<endl;
	file<<monhoc.MaMh<<endl;
	file<<monhoc.TenMh<<endl;
	file<<monhoc.TH<<endl;
	file.close();
}
void SaveDataMonHoc(ListMonHoc lmh)
{
	SaveDataMonHoc(lmh->left);
	  SaveData_Mh(lmh->monhoc);
	  SaveDataMonHoc(lmh->right);
}
int LoadData_Mh(ListMonHoc &lmh)
{
	fstream file; 
	file.open("DSMH.DAT",ios::in);
	
	if (!file.is_open())
		return 0;
    while(!file.eof())
	{   MonHoc mh;
        file.getline(mh.LT,255);
		file.getline(mh.MaMh,255);
		file.getline(mh.TenMh,255);
		file.getline(mh.TH,255);
		InsertMonHoc(lmh,mh);
	}
 file.close();
	return 1;
}
void RemoveMon(ListMonHoc &lmh)
{
	if(lmh->left!=NULL)
	  RemoveMon(lmh->left);
	else {
		ListMonHoc tam;
		tam->monhoc=lmh->monhoc;
		tam=lmh;
		lmh=tam->right;
	}
}
int XoaMon(ListMonHoc &lmh,char a[])
{
	if(lmh==NULL) return 0;
	if(strcmp(a,lmh->monhoc.MaMh)==-1) XoaMon(lmh->left,a);
	if(strcmp(a,lmh->monhoc.MaMh)==1)  XoaMon(lmh->right,a);
	if(strcmp(a,lmh->monhoc.MaMh)==0)
	{
		ListMonHoc tam;
		tam=lmh;
		if(tam->right==NULL) lmh=tam->left;
		else if(tam->left==NULL)
		 lmh=tam->right;
		 else RemoveMon(tam->right);
		 delete tam;
	}
	return 1;
}

/*void SapXepMonHoc(ListMonHoc &lmh)
{
    MonHoc temp;
	for (int i = 0;i < lmh.n - 1;i++)
	{
		for (int j = i+1; j < lmh.n; j++)
		{
			if(strcmp(lmh.DanhSachMon[i].TenMh,lmh.DanhSachMon[j].TenMh) == 1){
				temp = lmh.DanhSachMon[i];
				lmh.DanhSachMon[i] = lmh.DanhSachMon[j];
				lmh.DanhSachMon[j] = temp;
			}
		}
	}
}*/

// DATA FILE LOP
void SaveData_LopTC(ListLopTinChi llh)
{
	fstream FileOut_LopTC;
	FileOut_LopTC.open("DSLTC.DAT", ios::out | ios::binary | ios::trunc);
	
	for(int i = 0; i < llh.n;i++)
	{
		FileOut_LopTC.write((char*)(&llh.LLTC[i]), sizeof(LopTinChi));
	}	
	FileOut_LopTC.close();
}

int LoadData_Lop(ListLopTinChi &lltc)
{
	lltc.n = 0;
	LopTinChi ltc;
	fstream FileIn_LopTC;	
	FileIn_LopTC.open("DSLTC.DAT", ios::in | ios::binary);
	
	if (!FileIn_LopTC.is_open())
		return 0;
	
	while(FileIn_LopTC.read((char*)(&ltc), sizeof(LopTinChi)))
	{
			lltc.LLTC[lltc.n] = ltc;
			lltc.n++;	
	}
	FileIn_LopTC.close();
	return 1;
}

/*	DATA FILE MON HOC 
void SaveData_Mh(ListMonHoc lmh)
{
	fstream FileOut_Mh;
	FileOut_Mh.open("DSMH.DAT",ios::out|ios::binary | ios::trunc );

	for (int i = 0;i < lmh.n;i++)
	{
		FileOut_Mh.write((char*)(&lmh.DanhSachMon[i]), sizeof(MonHoc));	
	}
	FileOut_Mh.close();
}

int LoadData_Mh(ListMonHoc &lmh)
{
	lmh.n = 0;
	MonHoc mh;
	fstream FileIn_Mh; 
	FileIn_Mh.open("DSMH.DAT",ios::in|ios::binary );
	
	if (!FileIn_Mh.is_open())
		return 0;
	
	while (FileIn_Mh.read( (char*) &mh ,sizeof(MonHoc)))
	{
		lmh.DanhSachMon[lmh.n] = mh;
		lmh.n ++;
	}
	FileIn_Mh.close();
	return 1;
}*/

// DATA FILE SINH VIEN

void SaveData_Sv(ListSv &l)
{
	fstream FileOut_Sv;
	FileOut_Sv.open("DSSV.DAT",ios::out | ios::binary | ios::trunc);

		for (NodeSv *p = l.head ; p != NULL; p = p->next)
		{
			FileOut_Sv.write((char*)(&(p->info)), sizeof(SinhVien));
		}

	FileOut_Sv.close();
}

void ThemCuoi(ListSv &l, NodeSv *p){
	l.head->next = p;
	l.head = p;
}
int LoadData_Sv( ListSv &l)
{
	SinhVien sv;
	fstream FileIn_Sv;
	int dem;
	FileIn_Sv.open("DSSV.DAT" ,ios::in |ios::binary);
	
	if (!FileIn_Sv.is_open())
		return 0;
	
	l.head=NULL;
	
	while (FileIn_Sv.peek() != EOF)
	{
		FileIn_Sv.read((char*)(&sv), sizeof(SinhVien));
		ThemSvVaoSau(l,sv);
	}
	FileIn_Sv.close();
	return 1;
}

// DATA FILE DIEM

void SaveData_Diem(ListLopTinChi llh)
{
	fstream FileOut_Diem;
	FileOut_Diem.open("DSDIEM.DAT",ios::out | ios::binary | ios::trunc);		
	
	for (int i = 0 ; i < llh.n ; i++)
	{
		for(ListSvPtr p = llh.LLTC[i].First; p != NULL; p = p->next)
		{
			int sldiem = DemSoLuongDiem(p->info.First);
			FileOut_Diem.write((char*)&sldiem, sizeof(int));
																
			for(ListDiemPtr u = p->info.First; u != NULL; u = u->next)
			{
				FileOut_Diem.write((char*)(&(u->data)), sizeof(Diem));
			}			
		}
	}
	
	FileOut_Diem.close();
}

int LoadData_Diem(ListLopTinChi &llh)
{
	fstream FileIn_Diem;
	FileIn_Diem.open("DSDIEM.DAT", ios::in | ios::binary);	
	Diem d;	
	
	for(int i = 0; i<llh.n; i++)
	{
		for(ListSvPtr p = llh.LLTC[i].First; p != NULL; p = p->next)
		{
			p->info.First = NULL;
		}
	}
	
	if (!FileIn_Diem.is_open())
		return 0;
	
	int sldiem;

	for (int i = 0 ; i < llh.n ; i++)
	{
		if(llh.LLTC[i].First == NULL)
			continue;
		for(ListSvPtr p = llh.LLTC[i].First; p != NULL; p = p->next)
		{
			FileIn_Diem.read((char*)(&sldiem), sizeof(int));
				
			for(int i = 0; i < sldiem; i++)
			{
				FileIn_Diem.read((char*)(&d), sizeof(Diem));
				ThemDiemVaoDau(p->info.First,d);
			}
		}
	}
	
	FileIn_Diem.close();
	return 1;
}

//----------------------------------------------------

void box(int x,int y,int dodai, int docao){
	gotoxy(x,y);
	cout << char(201);
	for(int i=0;i<dodai;i++)
		cout << char(205);
	cout<<char(187);
	for(int i=1;i<docao;i++){
		gotoxy(x,y+i);
		cout<<char(186);
		gotoxy(x+dodai+1,y+i);
		cout<<char(186);
	}
	gotoxy(x,y+docao);
	cout << char(200);
	for(int i=0;i<dodai;i++)
		cout << char(205);
	cout<<char(188);
}

void thanh_ngang(int x, int y, int dodai){
	gotoxy(x,y);
	for(int i=0;i<dodai;i++)
		cout<<char(196);
}
void thanh_doc(int x,int y,int docao){
	for(int i=0;i<docao;i++){
		gotoxy(x,y+i);
		cout<<char(179);
	}
	
}
void TableSv()
{	

	gotoxy(tabx, taby);	cout << char(218);
	for(int i = 1; i < tabs + 1; i++) cout << char(196);
	cout << char(191);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx, taby + i); cout << char(179);
	}
	
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + tabs + 1, taby + i); cout << char(179);
	}		
	gotoxy(tabx, taby + tabh); cout << char(192);
	for(int i = 1; i < tabs + 1; i++)
	{
		gotoxy(tabx + i, taby + tabh); cout << char(196);
	}
	gotoxy(tabx + tabs+1,taby +tabh); cout <<char(217);
	gotoxy(tabx + 71, taby + tabh + 1);	cout<<" Tiep Theo ["<<(char)ArrowDOWN<<"]";
	//--------------------------------------
	//STT
	gotoxy(tabx +2, taby + 1); cout <<"STT";
	gotoxy(tabx + 6, taby); cout << char(194);	
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 6, taby + i); cout << char(179);
	}	
	gotoxy(tabx, taby +2); cout << char(195);	
	
	for(int i = 1; i < tabs + 1; i++) 
	{
		gotoxy(tabx +i, taby +2); cout << char(196);
	}
	gotoxy(tabx+ 6, taby +2); cout << char(197);
	gotoxy(tabx+ tabs +1, taby +2); cout << char(180);
	gotoxy(tabx+ 6, taby + tabh); cout << char(193);
	// Massv
	gotoxy(tabx +11, taby + 1); cout <<"MA SV";
	gotoxy(tabx + 20, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 20, taby + i); cout << char(179);
	}
	gotoxy(tabx+20, taby +2); cout << char(197);
	gotoxy(tabx+ 20, taby + tabh); cout << char(193);
	
	// Ho
	gotoxy(tabx +33, taby + 1); cout <<"HO SV";
	gotoxy(tabx + 50, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 50, taby + i); cout << char(179);
	}
	gotoxy(tabx+50, taby +2); cout << char(197);
	gotoxy(tabx+ 50, taby + tabh); cout << char(193);
	//Ten
	gotoxy(tabx +54, taby + 1); cout <<"TEN SV";
	gotoxy(tabx + 63, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 63, taby + i); cout << char(179);
	}
	gotoxy(tabx+63, taby +2); cout << char(197);
	gotoxy(tabx+ 63, taby + tabh); cout << char(193);
	//Phai
	gotoxy(tabx +66, taby + 1); cout <<"GT";
	gotoxy(tabx + 70, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 70, taby + i); cout << char(179);
	}
	gotoxy(tabx+70, taby +2); cout << char(197);
	gotoxy(tabx+ 70, taby + tabh); cout << char(193);
	// SDT
	gotoxy(tabx +76, taby + 1); cout <<"SDT";
}

void TableLop()
{
	gotoxy(tabx, taby);	cout << char(218);
	for(int i = 1; i < tabs + 1; i++) cout << char(196);
	cout << char(191);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx, taby + i); cout << char(179);
	}
	
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + tabs + 1, taby + i); cout << char(179);
	}		
	gotoxy(tabx, taby + tabh); cout << char(192);
	for(int i = 1; i < tabs + 1; i++)
	{
		gotoxy(tabx + i, taby + tabh); cout << char(196);
	}
	gotoxy(tabx + tabs+1,taby +tabh); cout <<char(217);
	
	gotoxy(tabx + 71, taby + tabh + 1);	cout<<" Tiep Theo ["<<(char)ArrowDOWN<<"]";
	//--------------------------------------
	//STT
	gotoxy(tabx +3, taby + 1); cout <<"STT";
	gotoxy(tabx + 8, taby); cout << char(194);	
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 8, taby + i); cout << char(179);
	}	
	gotoxy(tabx, taby +2); cout << char(195);	
	
	for(int i = 1; i < tabs + 1; i++) 
	{
		gotoxy(tabx +i, taby +2); cout << char(196);
	}
	gotoxy(tabx+ 8, taby +2); cout << char(197);
	gotoxy(tabx+ tabs +1, taby +2); cout << char(180);
	gotoxy(tabx+ 8, taby + tabh); cout << char(193);
	// Ma lop
	gotoxy(tabx +5, taby + 1); cout <<"MA LOP";
	gotoxy(tabx + 35, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 35, taby + i); cout << char(179);
	}
	gotoxy(tabx+35, taby +2); cout << char(197);
	gotoxy(tabx+ 35, taby + tabh); cout << char(193);
	
	// TEN LOP
	gotoxy(tabx +25, taby + 1); cout <<"TEN LOP";
	gotoxy(tabx + 70, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 70, taby + i); cout << char(179);
	}
	gotoxy(tabx+70, taby +2); cout << char(197);
	gotoxy(tabx+ 70, taby + tabh); cout << char(193);
	//Nien khoa
	gotoxy(tabx +45, taby + 1); cout <<"NIEN KHOA";
	
	//test
	gotoxy(tabx +55, taby + 1); cout <<"HOC KY";
	gotoxy(tabx +65, taby + 1); cout <<"NHOM";
	gotoxy(tabx +75, taby + 1); cout <<"SV MIN";
	gotoxy(tabx +85, taby + 1); cout <<"SV MAX";
} 

void TableMon()
{
	gotoxy(18,2); cout <<" DANH SACH TAT CA MON HOC DANG DUOC DUA VAO CHUONG TRINH DAO TAO ";
	
	gotoxy(tabx, taby);	cout << char(218);
	for(int i = 1; i < tabs + 1; i++) cout << char(196);
	cout << char(191);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx, taby + i); cout << char(179);
	}
	
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + tabs + 1, taby + i); cout << char(179);
	}		
	gotoxy(tabx, taby + tabh); cout << char(192);
	for(int i = 1; i < tabs + 1; i++)
	{
		gotoxy(tabx + i, taby + tabh); cout << char(196);
	}
	gotoxy(tabx + tabs+1,taby +tabh); cout <<char(217);
	
	gotoxy(tabx + 71, taby + tabh + 1);	cout<<" Tiep Theo ["<<(char)ArrowDOWN<<"]";
	//--------------------------------------
	//STT
	gotoxy(tabx +3, taby + 1); cout <<"STT";
	gotoxy(tabx + 8, taby); cout << char(194);	
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 8, taby + i); cout << char(179);
	}	
	gotoxy(tabx, taby +2); cout << char(195);	
	
	for(int i = 1; i < tabs + 1; i++) 
	{
		gotoxy(tabx +i, taby +2); cout << char(196);
	}
	gotoxy(tabx+ 8, taby +2); cout << char(197);
	gotoxy(tabx+ tabs +1, taby +2); cout << char(180);
	gotoxy(tabx+ 8, taby + tabh); cout << char(193);
	// Ma mon hoc
	gotoxy(tabx +13, taby + 1); cout <<"MA MON HOC";
	gotoxy(tabx + 27, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 27, taby + i); cout << char(179);
	}
	gotoxy(tabx+27, taby +2); cout << char(197);
	gotoxy(tabx+ 27, taby + tabh); cout << char(193);
	
	// TEN MON HOC
	gotoxy(tabx +40, taby + 1); cout <<"TEN MON HOC";
	gotoxy(tabx + 62, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 62, taby + i); cout << char(179);
	}
	gotoxy(tabx+62, taby +2); cout << char(197);
	gotoxy(tabx+ 62, taby + tabh); cout << char(193);
	//TC LT
	gotoxy(tabx +63, taby + 1); cout <<"TIN CHI LT";
	gotoxy(tabx + 73, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 73, taby + i); cout << char(179);
	}
	gotoxy(tabx+73, taby +2); cout << char(197);
	gotoxy(tabx+ 73, taby + tabh); cout << char(193);
	//Phai
	gotoxy(tabx +74, taby + 1); cout <<"TIN CHI TH";
	
	
	
}
void TableDiemNhap(int lan)
{
	
	gotoxy(tabx, taby);	cout << char(218);
	for(int i = 1; i < tabs + 1; i++) cout << char(196);
	cout << char(191);
	for(int i = 1; i < tabh +1; i++) 
	{
		gotoxy(tabx, taby + i); cout << char(179);
	}
	
	for(int i = 1; i < tabh +1; i++) 
	{
		gotoxy(tabx + tabs + 1, taby + i); cout << char(179);
	}		
	gotoxy(tabx, taby + tabh); cout << char(192);
	for(int i = 1; i < tabs + 1; i++)
	{
		gotoxy(tabx + i, taby + tabh); cout << char(196);
	}
	gotoxy(tabx + tabs+1,taby +tabh); cout <<char(217);
	
	gotoxy(tabx + 71, taby + tabh + 1);	cout<<" Tiep Theo ["<<(char)ArrowDOWN<<"]";
	
	//--------------------------------------
	
	//STT
	gotoxy(tabx +2, taby + 1); cout <<"STT";
	gotoxy(tabx + 6, taby); cout << char(194);	
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 6, taby + i); cout << char(179);
	}	
	gotoxy(tabx, taby +2); cout << char(195);	
	
	for(int i = 1; i < tabs + 1; i++) 
	{
		gotoxy(tabx +i, taby +2); cout << char(196);
	}
	gotoxy(tabx+ 6, taby +2); cout << char(197);
	gotoxy(tabx+ tabs +1, taby +2); cout << char(180);
	gotoxy(tabx+ 6, taby + tabh); cout << char(193);
	// Ma Sv
	gotoxy(tabx +9, taby + 1); cout <<"MA SINH VIEN";
	gotoxy(tabx + 23, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 23, taby + i); cout << char(179);
	}
	gotoxy(tabx+23, taby +2); cout << char(197);
	gotoxy(tabx+ 23, taby + tabh); cout << char(193);
	
	// HO
	gotoxy(tabx +33, taby + 1); cout <<"HO SINH VIEN";
	gotoxy(tabx + 53, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 53, taby + i); cout << char(179);
	}
	gotoxy(tabx+53, taby +2); cout << char(197);
	gotoxy(tabx+ 53, taby + tabh); cout << char(193);
	// Ten
	gotoxy(tabx +56, taby + 1); cout <<"TEN SINH VIEN";
	gotoxy(tabx + 71, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 71, taby + i); cout << char(179);	
	}
	gotoxy(tabx+71, taby +2); cout << char(197);
	gotoxy(tabx+ 71, taby + tabh); cout << char(193);
	
	//DIEM LAN 1
	gotoxy(tabx +73, taby + 1); cout <<"DIEM LAN "<< lan;
	
	
}

void TableDiem2Lan()
{
	
	gotoxy(tabx, taby);	cout << char(218);
	for(int i = 1; i < tabs + 1; i++) cout << char(196);
	cout << char(191);
	for(int i = 1; i < tabh +1; i++) 
	{
		gotoxy(tabx, taby + i); cout << char(179);
	}
	
	for(int i = 1; i < tabh +1; i++) 
	{
		gotoxy(tabx + tabs + 1, taby + i); cout << char(179);
	}		
	gotoxy(tabx, taby + tabh); cout << char(192);
	for(int i = 1; i < tabs + 1; i++)
	{
		gotoxy(tabx + i, taby + tabh); cout << char(196);
	}
	gotoxy(tabx + tabs+1,taby +tabh); cout <<char(217);
	
	gotoxy(tabx + 71, taby + tabh + 1);	cout<<" Tiep Theo ["<<(char)ArrowDOWN<<"]";
	
	//--------------------------------------
	
	//STT
	gotoxy(tabx +2, taby + 1); cout <<"STT";
	gotoxy(tabx + 6, taby); cout << char(194);	
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 6, taby + i); cout << char(179);
	}	
	gotoxy(tabx, taby +2); cout << char(195);	
	
	for(int i = 1; i < tabs + 1; i++) 
	{
		gotoxy(tabx +i, taby +2); cout << char(196);
	}
	gotoxy(tabx+ 6, taby +2); cout << char(197);
	gotoxy(tabx+ tabs +1, taby +2); cout << char(180);
	gotoxy(tabx+ 6, taby + tabh); cout << char(193);
	// Ho
	gotoxy(tabx +15, taby + 1); cout <<"HO SINH VIEN";
	gotoxy(tabx + 36, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 36, taby + i); cout << char(179);
	}
	gotoxy(tabx+36, taby +2); cout << char(197);
	gotoxy(tabx+ 36, taby + tabh); cout << char(193);
	
	// Ten
	gotoxy(tabx +41, taby + 1); cout <<"TEN SINH VIEN";
	gotoxy(tabx + 58, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 58, taby + i); cout << char(179);
	}
	gotoxy(tabx+58, taby +2); cout << char(197);
	gotoxy(tabx+ 58, taby + tabh); cout << char(193);
	// Diem lan 1
	gotoxy(tabx +60, taby + 1); cout <<"DIEM LAN 1";
	gotoxy(tabx + 71, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 71, taby + i); cout << char(179);	
	}
	gotoxy(tabx+71, taby +2); cout << char(197);
	gotoxy(tabx+ 71, taby + tabh); cout << char(193);
	
	//DIEM LAN 2
	gotoxy(tabx +73, taby + 1); cout <<"DIEM LAN 2";
	
	
}

void Box_NhapMon()
{
	gotoxy(boxx, boxy); 
	box(boxx, boxy, 40,20);
	gotoxy(boxx, boxy + 2); cout << char(186)<< "    Nhap vao ma mon hoc: ";
	gotoxy(boxx, boxy + 4); cout << char(186)<< "   Nhap vao ten mon hoc: ";
	gotoxy(boxx, boxy + 6); cout << char(186)<< " Nhap vao so tin chi LT: ";
	gotoxy(boxx, boxy + 8); cout << char(186)<< " Nhap vao so tin chi TH: ";
}

void Box_NhapSv()
{
	
	gotoxy(boxx, boxy + 2); cout << "  Nhap vao ma lop: ";
	gotoxy(boxx, boxy + 4); cout << "  Nhap vao ma sinh vien: ";
	gotoxy(boxx, boxy + 6); cout << "  Nhap vao ho sinh vien: ";
	gotoxy(boxx, boxy + 8); cout << " Nhap vao ten sinh vien: ";
	gotoxy(boxx, boxy + 10); cout << "         Chon gioi tinh: ";
	gotoxy(boxx, boxy + 12); cout <<" Nhap vao so dien thoai: ";
}

void Box_NhapLop()
{
	box(boxx-1,boxy,60,14);
	gotoxy(boxx, boxy + 2); cout << " Nhap vao ma lop: ";
	gotoxy(boxx, boxy + 4); cout << " Nhap vao nien khoa: ";
	gotoxy(boxx, boxy + 6); cout <<" Nhap vao hoc ky: ";
	gotoxy(boxx, boxy + 8); cout <<" Nhap vao nhom: ";
	gotoxy(boxx, boxy + 10); cout <<" So sinh vien Min: ";
	gotoxy(boxx, boxy + 12); cout <<" So sinh vien Max: ";
}

string Fix_HoTen(string t)
{
	if(t != "") {                   // t co ki tu
		while(t[0] == ' ') t.erase(0, 1); //Xoa 1 khoang trang o dau
		while(t[t.length() - 1] == ' ') t.erase(t.length() - 1, 1); //Xoa khoang trang o cuoi
		if(t.length() > 3) //Xoa khoang trang thua o giua , vi 4 ki tu tro len moi tao ra 2khoang trang thua`
			for(short i = 1; i < t.length() - 2; i++)
				if(t[i] == ' ' && t[i+1] == ' ') {
					t.erase(i, 1);  i--;
				}
		for(short i = 0; i < t.length(); i++)
		 if(t[i] >= 'A' && t[i] <= 'Z') t[i] += 32; //Chuyen chu hoa thanh chu thuong
		
		if(t[0] >= 'a' && t[0] <= 'z') // neu ki tu dau tien la chu
		{
			t[0] = t[0] - 32; //Chuyen ki tu dau thanh chu hoa
		}
		for(int i = 1;i < t.length();i++) // In hoa chu cai dau tien sau moi khoang trang
		{
			if(t[i] == ' ')
			{
				if(t[i+1] >= 'a' && t[i+1] <= 'z')
				{
					t[i+1] -= 32;
					i = i+1;
				}
			}
		}
	}
	return t;
}

string Fix_Ma(string t)
{
	if(t != "") {                   // t co ki tu				
		//Chuyen chu thuong thanh chu hoa	
		for(int i = 0; i < t.length(); i++)
		 if(t[i] >= 'a' && t[i] <= 'z')
		 	t[i] -= 32; 
	}
	return t;
}

void Print_Textfield(string t, int n, int x)
{
	gotoxy(boxx + x, boxy + n);
	if(t.length() >= boxs - x) {
		for(int i = 0; i < boxs - x; i++) cout << t[i];
	}
	else if(t.length() < boxs - x) {
		cout << t;
		for(int i = 0; i < boxs - x - t.length(); i++) cout << " ";
	}
}

void Del_Error() {
	gotoxy(boxx, 29);
	for(int i = 0; i < 70; i++) cout << " ";
}

int CheckError_Lop(ListLopTinChi llh, string t, int k)
{
	if(k == 1) t = Fix_Ma(t);
	if(k == 2) t = Fix_HoTen(t);
	char temp[31];
	strcpy(temp,t.c_str());
	
	if(k == 1) {
		if(t == "") return 5;
		else if(t.length() < 10) return 5;
	}
	else if(k == 2) {
		if(t == "") return 5;
		else if(t.length() < 4) return 5;
	}
	else if(k == 3) {
		if(t == "") return 5;
	}
	else if(k == 4) {
		if(t == "") return 5;
	}
	else if(k == 5) {
		if(t == "") return 5;
	}
	else if(k == 6) {
		if(t == "") return 5;
	}
	return 0;
}

int CheckError_Sv(ListLopTinChi llh, string t, int k, int vitrilop)
{
	if(k == 1) t = Fix_Ma(t);
	if(k == 2 || k == 3) t = Fix_HoTen(t);
	
	char temp[30];
	strcpy(temp,t.c_str());
	
	if(k == 1) {
		if(t == "") return 1;
		else if (CheckMaSv_Truong(llh, temp) != NULL) return 2;
		else if(t.length() <11) return 3;
	}
	else if(k == 2) {
		if(t == "") return 4;
	}
	else if(k == 3) {
		if(t == "") return 5;
	}
	else if(k == 5) {
		if(t == "") return 6;
		else if(t.length() <10) return 7;
	}
	
	return 0;
}

int CheckError_Monhoc(ListMonHoc lmh, string t, int k) {
	if(k == 1) t = Fix_Ma(t);
	if(k == 2) t = Fix_HoTen(t);
	char temp[31];
	strcpy(temp,t.c_str());
	
	if(k == 1) {
		if(t == "") return 1;
		else if(CheckMaMh(lmh, temp)!= -1) return 2;
	}
	else if(k == 2) {
		if(t == "") return 3;
	}
	else if(k == 3) {
		if(t == "") return 4;
	}
	else if(k == 4) {
		if(t == "") return 5;
	}
	return 0;
}

void Print_Error_Monhoc(int k){
	Del_Error();
	string s;
	if(k == 1) s = "MA MON HOC LA BAT BUOC! ";
	else if(k == 2) s = "MA MON HOC DA BI TRUNG! ";
	else if(k == 3) s = "TEN MON HOC LA BAT BUOC! ";	
	gotoxy(boxx + 15 , 29); cout << " CANH BAO: "<<s;
	if(k == 4){
		gotoxy(boxx + 15 , 29);
		cout << " VUI LONG NHAP SO TIN CHI LY THUYET! ";
	}
	else if(k == 5)
	{
		gotoxy(boxx + 15 , 29);
		cout << " VUI LONG NHAP SO TIN CHI THUC HANH! ";		
	}

}

void Print_Error_Lop(int k)
{
	Del_Error();
	string s;
	if(k == 1) s = "VUI LONG NHAP MA MON HOC! ";
	else if(k == 2) s = "MA MON HOC DA BI TRUNG! ";
	else if(k == 3) s = "MA MON HOC KHONG TON TAI!! ";
	else if(k == 4) s = "TEN MON HOC LA BAT BUOC! ";
	else if(k == 5) s = "VUI LONG NHAP THONG TIN HOP LE! ";
	else if(k == 6) s = "NIEN KHOA KHONG HOP LE! ";
	else if(k == 7) s = "VUI LONG NHAP HOC KI";
	gotoxy(boxx + 15 , 30); cout << " CANH BAO: "<<s;
}

void Print_Error_Sv(int k)
{
	Del_Error();
	string s;
	if(k == 1) s = "MA SINH VIEN LA BAC BUOC! ";
	else if(k == 2) s = "MA SINH VIEN DA BI TRUNG! ";
	else if(k == 3) s = "MA SINH VIEN KHONG HOP LE! ";
	else if(k == 4) s = "HO SINH VIEN LA BAT BUOC! ";
	else if(k == 5) s = "TEN SINH VIEN LA BAT BUOC! ";
	else if(k == 6) s = "SO DIEN THOAI LA BAT BUOC! ";
	else if(k == 7) s = "SO DIEN THOAI KHONG HOP LE! ";
	gotoxy(boxx + 15 , 29); cout << "CANH BAO: "<<s;
}

void Frame_Lop(int x, int y)
{
	int n;
	gotoxy(x,y);     cout <<"      Nhap Vao Ma Lop      ";
}

void Box_XacnhanLuu()
{
	int x = 31, y = 17, h = 6, s = 36;
	
	// doi mau tab
	for(int j = 0;j<h;j++)
	for(int i = 0;i< s;i++){
		gotoxy(x+i,y + j); ; cout <<" ";
	}
	// khung
	 gotoxy(x+3,y+1); cout <<"BAN CO MUON LUU VAO FILE KHONG?";
	gotoxy(x, y);	cout << char(218);
	for(int i = 1; i < s; i++) cout << char(196);
	cout << char(191);
	for(int i = 1; i < h; i++) 
	{
		gotoxy(x, y + i); cout << char(179);
	}
	
	for(int i = 1; i < h + 1; i++)
	{
	
		gotoxy(x + s, y + i); cout << char(179);
	}
		
	gotoxy(x, y + h); cout << char(192);
	
	for(int i = 1; i < s ; i++)
	{
		gotoxy(x + i, y + h); cout << char(196);
	}
	
	gotoxy(x + s,y+h); cout <<char(217);
	
	// nut co
	Raw_Button_YES(70);
	
	// nut khong
	Raw_Button_NO(70);
		
}

bool XacNhanLuuFile()
{
	int keyhit;
	Box_XacnhanLuu();
	Raw_Button_NO(78);
	bool Luu = false;
	
	while(keyhit != ENTER){
		keyhit = getch();
		if(keyhit == 224)
		{
			keyhit = getch();
			if(keyhit == LEFT){
				if(Luu == true)
				{
					Raw_Button_YES(70);
					Raw_Button_NO(78);
					Luu = false;		
				}	
				else {
					Raw_Button_YES(78);
					Raw_Button_NO(70);
					Luu = true;
				}
			}
			else if(keyhit == RIGHT){
				if(Luu == false)
				{
					Raw_Button_YES(78);
					Raw_Button_NO(70);
					Luu = true;							
				}	
				else {
					Raw_Button_YES(70);
					Raw_Button_NO(78);
					Luu = false;
				}
			}					
		}
		if(keyhit == ENTER)
			return Luu;		
	}
}
int dieukhien(char keyhit,int &dc, int TongLop, int &TrangHienTai,int &n){
	if (keyhit==72){
		gotoxy(tabx,taby+dc+3); printf(" ");
		dc--;
		if(dc<0)	dc++;
		gotoxy(tabx, taby +dc+3);
		printf(">");
	}
	else if (keyhit==80){
		gotoxy(tabx,taby+dc+3); printf(" ");
		dc++;
		if(dc>TongLop%21-1)	dc--;
		gotoxy(tabx, taby +dc+3);
		printf(">");
	}
	else if (keyhit== 73)							
		if(TongLop/21 > 1 && TrangHienTai > 0){
			system("cls");
			TrangHienTai--;
		}												
	else if (keyhit== 81)					
		if(TongLop > 1 && TrangHienTai + 1 < TongLop/21){
			TrangHienTai++;
			system("cls");
		}	
	n=dc+TrangHienTai*20;
	if(n>TongLop) n--;
	return n;
}
void NhapLop(ListLopTinChi &lltc)
{
	int fix=-1;
NhapL:
	system("cls");
	int check = 0;
	LoadData_Lop(lltc);
//	LoadData_Sv(llh);
	
	char keyhit;
	int x = 22;
	int k,demkitu,ngaunhien;
	char checkdel[11];

	while(lltc.n <= MAXLISTLH)
	{
		system("cls");
		Box_NhapLop();
				
		LopTinChi ltc;

		string text; 
		string field[6];
		field[0] = ""; // ma mon hoc
		field[1] = ""; // nien khoa	
		field[2] = ""; // hoc ki
		field[3] = ""; // nhom
		field[4] = ""; // so sv min
		field[5] = ""; // so sv max

		for(int n = 2, k = 1; k < 7; k++, n+=2)
		{
		if(fix>=0){
			string field0( lltc.LLTC[fix].MaMh ); // tai sao phai them std:: ???
			field[0]=field0;
			std::string field1(lltc.LLTC[fix].NienKhoa);
			field[1]=field1;
			std::string field2(lltc.LLTC[fix].HocKy);
			field[2]=field2;
			std::string field3(lltc.LLTC[fix].Nhom);
			field[3]=field3;
			std::string field4(lltc.LLTC[fix].SvMin);
			field[4]=field4;
			std::string field5(lltc.LLTC[fix].SvMax);
			field[5]=field5;
		}
			keyhit = 0;
			demkitu = field[k-1].length();
			text = field[k-1]; 
			while(keyhit != ENTER)
			{		 	
				gotoxy(boxx + x , boxy + n);
				if(demkitu < boxs - x)
				{
					cout << text; 
					for(int i = 1; i < boxs - (x - 1) - demkitu; i++) cout << " ";
				}
				else
				{
					for(int i = demkitu - boxs + x; i < demkitu; i++) cout << text[i];
				}
		
				gotoxy(boxx + x + demkitu , boxy + n); //Xuat ra vi tri con tro khi khung nhap chua bi tran
				keyhit = getch();
				if(keyhit == 96) goto CHECKDSLOP;
				if(keyhit == -32)
				{
					keyhit = getch();
					if(keyhit == UP)
					{
					 	if(k == 1) text = Fix_HoTen(text);		
						Print_Textfield(text,n,x);
						field[k-1] = text;
						Del_Error();
						if(k > 1){
						n-=2;
						k--;}
						demkitu = field[k-1].length();
						text = field[k-1];
					}
					else if(keyhit == DOWN)
					 {
						if(CheckError_Lop(lltc,text , k) != 0){
					 		keyhit = 0;
							Print_Error_Lop(CheckError_Lop(lltc, text, k));
							if(k == 1) text = Fix_Ma(text);				
							Print_Textfield(text,n,x);							
						}
						else {
					 		if(k == 1) text = Fix_Ma(text);			
							Print_Textfield(text,n,x);
							field[k-1] = text;
							Del_Error();
							if(k < 6){
							n+=2;
							k++;}
							demkitu = field[k-1].length();
							text = field[k-1];
						}
					}
				}
				else if(keyhit == BACKSPACE)
				 {
					if(demkitu > 0)
					{
						demkitu--;
						text = text.substr(0, text.size() - 1);
						Del_Error();
					}				
				}
				else if((keyhit > 96 && keyhit < 123) || (keyhit > 64 && keyhit < 91) || (keyhit == 45)|| (keyhit == 32)|| (keyhit > 47 && keyhit < 58)) {		
					if((keyhit == 45) || (keyhit > 96 && keyhit < 123) || (keyhit > 64 && keyhit < 91) || (keyhit > 47 && keyhit < 58))
						if(k == 1 && text.length() < 10){
							demkitu++;
							text += char(keyhit);
							Del_Error();
						}	
					if((keyhit == 32)||(keyhit > 47 && keyhit < 58))
						if(k == 2 && text.length() < 4){
							demkitu++;
							text += char(keyhit);
							Del_Error();
						}
					if((keyhit == 32)||(keyhit > 47 && keyhit < 58))
						if(k == 3 && text.length() < 1){
							demkitu++;
							text += char(keyhit);
							Del_Error();
						}
					if((keyhit == 32)||(keyhit > 47 && keyhit < 58))
						if(k == 4 && text.length() < 2){
							demkitu++;
							text += char(keyhit);
							Del_Error();
						}
					if((keyhit > 47 && keyhit < 58))
						if(k == 5 && text.length() < 3){
							demkitu++;
							text += char(keyhit);
							Del_Error();
						}
					if((keyhit == 32)||(keyhit > 47 && keyhit < 58))
						if(k == 6 && text.length() < 4){
							demkitu++;
							text += char(keyhit);
							Del_Error();
						}							
						// cau a									
				}
				else if(keyhit == ESC) break;
				else if(keyhit == ENTER){
					if(CheckError_Lop(lltc,text, k) != 0) {
						keyhit = 0;
						Print_Error_Lop(CheckError_Lop(lltc, text, k));
						if(k == 1) text = Fix_Ma(text);	
						Print_Textfield(text,n,x);
					}
				}
				else if(keyhit == 0)
				{
					keyhit = getch();
					if(keyhit == F5)
					{
						if(check !=0){
							Sleep(500);
							SapXepLopHoc(lltc);
							SaveData_LopTC(lltc);			
							check = 0;
							continue;
						}				
					}		
				}
			}
			if(keyhit == ESC)
			{	
				if(check != 0)
				{
					if(XacNhanLuuFile() == true)
					{
						SapXepLopHoc(lltc);
						SaveData_LopTC(lltc);
					}
				}
				else{					
					Sleep(500);
				}
				return;
			}
			if(k == 1) text = Fix_Ma(text);
	//
			Print_Textfield(text,n,x);
			field[k-1] = text;
			Del_Error();
		}		
		if(keyhit == ENTER)
		{
			strcpy(lltc.LLTC[lltc.n].MaMh, field[0].c_str());
			strcpy(lltc.LLTC[lltc.n].NienKhoa, field[1].c_str());
			strcpy(lltc.LLTC[lltc.n].HocKy, field[2].c_str());
			strcpy(lltc.LLTC[lltc.n].Nhom, field[3].c_str());
			strcpy(lltc.LLTC[lltc.n].SvMin, field[4].c_str());
			strcpy(lltc.LLTC[lltc.n].SvMax, field[5].c_str());
			lltc.LLTC[lltc.n].MaLopTC = Random(ngaunhien)+lltc.LLTC[lltc.n-1].MaLopTC;
			lltc.LLTC[lltc.n].First = NULL; // khoi tao danh sv lop do

			lltc.n++;
			check = 1;
			Sleep(500);
		}
	}
	
CHECKDSLOP:
	
	Sleep(500);
	system("cls");
	int TongLop = 0, TongTrang, TrangHienTai = 0;
	SapXepLopHoc(lltc);
	LopTinChi ltc[lltc.n];
			
	for(int i = 0; i < lltc.n; i++)
	{
			strcpy(ltc[TongLop].MaMh, lltc.LLTC[i].MaMh);
			strcpy(ltc[TongLop].NienKhoa, lltc.LLTC[i].NienKhoa);
			strcpy(ltc[TongLop].HocKy, lltc.LLTC[i].HocKy);
			strcpy(ltc[TongLop].Nhom, lltc.LLTC[i].Nhom);
			strcpy(ltc[TongLop].SvMin, lltc.LLTC[i].SvMin);
			strcpy(ltc[TongLop].SvMax, lltc.LLTC[i].SvMax);
			TongLop++;
	}

	TongTrang = (TongLop/21) + 1;
	int dc=0;
	int vitri=0;
	while(1)
	{					
		gotoxy(30,2); cout <<" DANH SACH CAC LOP TIN CHI      ";
		gotoxy(tabx + 38, taby + tabh + 1); cout <<"Trang "<< TrangHienTai + 1<<"/"<<TongTrang;
		if(TrangHienTai > 0){
			gotoxy(tabx + 58, taby + tabh + 1); cout<<"["<<(char)ArrowUP<<"]"<<" Tro Lai |";
		}
		for(int i = TrangHienTai * 20, j = 3; i < 20 + TrangHienTai * 20 && i <TongLop; i++, j++)
		{
			gotoxy(tabx +2 , taby + j); cout << lltc.LLTC[i].MaLopTC;
			gotoxy(tabx +15, taby + j); cout << ltc[i].MaMh;
			gotoxy(tabx +45, taby + j); cout << ltc[i].NienKhoa;
			gotoxy(tabx +55, taby + j); cout << ltc[i].HocKy;
			gotoxy(tabx +65, taby + j); cout << ltc[i].Nhom;
			gotoxy(tabx +75, taby + j); cout << ltc[i].SvMin;
			gotoxy(tabx +80, taby + j); cout << ltc[i].SvMax;
		}

		keyhit = getch();
		
		if(keyhit == -32){
			keyhit=getch();
			dieukhien( keyhit, dc, TongLop, TrangHienTai,vitri);
			if(keyhit==83){
			Delete_VT(lltc,vitri);
				goto CHECKDSLOP;
			}											
			}
		else if(keyhit==13)
			{
				fix=vitri;
				goto NhapL;
			}	
		else if(keyhit == ESC)
		{
			SaveData_LopTC(lltc);			
		}
		else if(keyhit == TAB)
		{
			if (check != 0){
					SaveData_LopTC(lltc);		
		 }
			goto NhapL;
		}				
		}
		fix=-1;
}

void NhapMon(ListMonHoc &lmh)
{
	system("cls");
	system("color 3e");
	
	int check = 0;
	while(lmh!=NULL)
	{
		system("cls");
		int keyhit, x = 26;
		int demkitu;
		string text;
		string field[4];
		field[0] = ""; // ma mon
		field[1] = "";	//ten mon
		field[2] = ""; // tin chi lt
		field[3] = ""; // tin chi th
		textcolor(59);
		Box_NhapMon();
		Raw_Button_ESC(color_text1, color_fill1);
		Raw_Button_OK(color_text1, color_fill1);
		textcolor(62);
		Monhoc tam;
		gotoxy(10,1);cout <<"    _   ____                   ____              __       _____            __  ";
		gotoxy(10,2);cout <<"   / | / / /_  ____ _____     / __ \\____ _____  / /_     / ___/____ ______/ /_ ";
		gotoxy(10,3);cout <<"  /  |/ / __ \\/ __ `/ __ \\   / / / / __ `/ __ \\/ __ \\    \\__ \\/ __ `/ ___/ __ \\";
		gotoxy(10,4);cout <<" / /|  / / / / /_/ / /_/ /  / /_/ / /_/ / / / / / / /   ___/ / /_/ / /__/ / / /";
		gotoxy(10,5);cout <<"/_/ |_/_/ /_/\\__,_/ .___/  /_____/\\__,_/_/ /_/_/ /_/   /____/\\__,_/\\___/_/ /_/ ";
		gotoxy(10,6);cout <<"                 /_/                                                    ";
		
		gotoxy(30,7); cout <<"    __  ___               __  __          ";
		gotoxy(30,8); cout <<"   /  |/  /___  ____     / / / /___  _____";
		gotoxy(30,9); cout <<"  / /|_/ / __ \\/ __ \\   / /_/ / __ \\/ ___/";
		gotoxy(30,10);cout <<" / /  / / /_/ / / / /  / __  / /_/ / /__  ";
		gotoxy(30,11);cout <<"/_/  /_/\\____/_/ /_/  /_/ /_/\\____/\\___/  ";
	
		for(int n = 2, k = 1; k < 5; k++, n+=2)
		{
			keyhit = 0;
			demkitu = field[k-1].length();
			text = field[k-1];
			while(keyhit != ENTER)
			 {
			 	if(check != 0)
					Raw_Button_F5(color_text1, color_fill1);
				else{
					gotoxy(45,31); textcolor(51); cout <<"        ";
					gotoxy(45,32); textcolor(51); cout <<"        ";
					gotoxy(45,33); textcolor(51); cout <<"        ";
					gotoxy(43,34); textcolor(51); cout <<"            ";
				}
				textcolor(62);
				gotoxy(boxx + x , boxy + n);
				if(demkitu < boxs - x)
				 {
					cout << text; 
					for(int i = 1; i < boxs - (x - 1) - demkitu; i++) cout << " ";
				 }
				else
				{
					for(int i = demkitu - boxs + x; i < demkitu; i++) cout << text[i];
				}
		
				 gotoxy(boxx + x + demkitu , boxy + n); //Xuat ra vi tri con tro khi khung nhap chua bi tran
	
				keyhit = getch();
				
				if(keyhit == 224)
				 {
					keyhit = getch();
					if(keyhit == UP){
						if(k == 1) text = Fix_Ma(text);
						if(k == 2) text = Fix_HoTen(text);				
								
						Print_Textfield(text, n,x);
						field[k-1] = text;
						Del_Error();
						if(k > 1){
						n-=2;
						k--;}
						demkitu = field[k-1].length();
						text = field[k-1];
					}
					
					else if(keyhit == DOWN)
					 {
						if(CheckError_Monhoc(lmh,text,k)!=0){
					 		keyhit = 0;
					 		Print_Error_Monhoc(CheckError_Monhoc(lmh,text,k));
					 	}
					 	else{
						 	if(k == 1) text = Fix_Ma(text);
					 		if(k == 2) text = Fix_HoTen(text);
					 		
							Print_Textfield(text, n,x);
							field[k-1] = text;
							Del_Error();
							if(k < 4) {
							n+=2;
							k++;}
							demkitu = field[k-1].length();
							text = field[k-1];
						}
					}
				}
				// TEST
					else if(keyhit == 96) {
						gotoxy(20,10);
						cout<<"asdasd";
					}
				else if(keyhit == BACKSPACE)
				 {
					if(demkitu > 0)
					{
						demkitu--;
						text = text.substr(0, text.size() - 1);
						Del_Error();
					}				
				}	
				else if((keyhit > 96 && keyhit < 123) || (keyhit > 64 && keyhit < 91) || (keyhit == 45)|| (keyhit == 32)|| (keyhit > 47 && keyhit < 58)) {		
					if((keyhit == 45) || (keyhit > 96 && keyhit < 123) || (keyhit > 64 && keyhit < 91) || (keyhit > 47 && keyhit < 58))
						if(k == 1 && text.length() < 10){
							demkitu++;
							text += char(keyhit);
							Del_Error();
						}
					if((keyhit == 32) || (keyhit > 96 && keyhit < 123) || (keyhit > 64 && keyhit < 91) || (keyhit > 47 && keyhit < 58))
						if(k == 2 && text.length() < 30){
							demkitu++;
							text += char(keyhit);
							Del_Error();
						}
					if (keyhit > 47 && keyhit < 58)
						if((k == 3 || k == 4) && text.length() < 2){					
							demkitu++;
							text += char(keyhit);
							Del_Error();
						}		
				}										
				else if(keyhit == ESC) break;	
			}
			if(keyhit == ESC) {
				if(check != 0){
					Raw_Button_ESC(color_text2, color_fill2);
					if(XacNhanLuuFile() == true)				
					{
										
						SaveDataMonHoc(lmh);
					}
					return;			
				}
				else{					
					Raw_Button_ESC(color_text2, color_fill2);
					Sleep(500);
					return;
				}
			}
			if(k == 1) text = Fix_Ma(text);
			if(k == 2) text = Fix_HoTen(text);					
			Print_Textfield(text,n,x);
			field[k-1] = text;
			Del_Error();
		}	
		if(keyhit == 13)
		{			
			strcpy(tam.MaMh,field[0].c_str());
			strcpy(tam.TenMh,field[1].c_str());
			strcpy(tam.LT,field[2].c_str());
			strcpy(tam.TH,field[3].c_str());
			InsertMonHoc(lmh,tam);
			
			check = 1;
			Raw_Button_OK(color_text2, color_fill2);
			Sleep(500);
		}
		else if (keyhit == ESC)
		{
			return;		
		}
	}	
}

char *CheckTontai_Lop(ListSv l)
{
CHECK:
	int x= 36, y = 13;
	
	int keyhit;
	int demkitu;
	SinhVien sv;
	string text,field;
	field = ""; // ma lop
	char temp[15];
	NodeSv *p;
	Frame_Lop(x,y);

	keyhit = 0;
	demkitu = field.length();
	text = field;
	while(1)
	{	
		gotoxy(x + 8, y + 1);
		if(demkitu < 10 ){
			cout << text;
			for(int i = 1; i < 11 - demkitu; i++) cout << " ";// xoa duoi
		}
		else
		{
			for(int i = demkitu - 10 ; i < demkitu; i++) cout << text[i];
		}
		if(demkitu < 10) gotoxy(x + 8 + demkitu, y + 1); //Xuat ra vi tri con tro
		keyhit = getch();
			
		if(keyhit == 224)
		{
			keyhit = getch();
			if(keyhit == DOWN || keyhit == UP || keyhit == LEFT || keyhit == RIGHT|| keyhit == INS || keyhit == DEL || keyhit == HOME || keyhit == END || keyhit == PGU || keyhit == PGD)
				continue;
		}
		else if(keyhit == BACKSPACE)
		{
			if(demkitu > 0)
			{
				demkitu--;
				text = text.substr(0, text.size() - 1);
				Del_Error();
			}	
		}
		else if(demkitu < 10 && ((keyhit == 45) || (keyhit > 96 && keyhit < 123) || (keyhit > 64 && keyhit < 91) || (keyhit > 47 && keyhit < 58))){		
				demkitu++;
				text += toupper(char(keyhit));
				Del_Error();	
		}
		else if(keyhit == ENTER){
			if(text == ""){
				Del_Error();
				keyhit = 0;
				gotoxy(boxx + 14 , 29);
				cout << " VUI LONG NHAP MA LOP DE KIEM TRA! ";
			}
			else {
				Del_Error();			
				strcpy(temp, text.c_str());			
					if(CheckMaLop(l,temp,p)!= NULL){
						gotoxy(boxx + 13 , 29);
						return temp;
						}	
					else {
						cout << " LOP KHONG TON TAI! XIN KIEM TRA LAI! ";
						goto CHECK;
					}
				}
					}
				
		else if(keyhit == ESC){
			return NULL;
		}				
	}
}

string GioiTinh()
{
	gotoxy(boxx + 26, boxy + 10); cout << " Nam ";
	gotoxy(boxx + 36, boxy + 10); cout << " Nu ";
	int keyhit;
	string gioitinh = "Nam";
	
		while(keyhit != ENTER){
		keyhit = getch();
		if(keyhit == 224)
		{
			keyhit = getch();
			if(keyhit == LEFT){
				Del_Error();
				if(gioitinh == "Nam")
				{
					gotoxy(boxx + 26, boxy + 10); cout << " Nam ";
					gotoxy(boxx + 36, boxy + 10); cout << " Nu ";	
					gioitinh = "Nu";		
				}	
				else if(gioitinh == "Nu"){
					gotoxy(boxx + 26, boxy + 10); cout << " Nam ";
					gotoxy(boxx + 36, boxy + 10);cout << " Nu ";	
					gioitinh = "Nam";
				}
			}
			else if(keyhit == RIGHT){
				Del_Error();
				if(gioitinh == "Nu")
				{
					gotoxy(boxx + 26, boxy + 10); textcolor(233); cout << " Nam ";
					gotoxy(boxx + 36, boxy + 10); textcolor(110); cout << " Nu ";	
					gioitinh = "Nam";							
				}	
				else if(gioitinh == "Nam"){
					gotoxy(boxx + 26, boxy + 10); textcolor(110); cout << " Nam ";
					gotoxy(boxx + 36, boxy + 10); textcolor(233); cout << " Nu ";	
					gioitinh = "Nu";
				}
			}
			else if(keyhit == UP || keyhit == DOWN){
				return gioitinh;
			}					
		}
		if(keyhit == ENTER){
			return gioitinh;
		}
		else if(keyhit == TAB)
			return "TAB";
		else if(keyhit == ESC)
			return "ESC";	
	}	
}
void NhapSv(ListSv &l)
{
	l.head = NULL;
	LoadData_Sv(l);
NHAP:
		system("cls");
		int vitrilop= 1, demsv = 0;
		Box_NhapSv();
		int keyhit, demkitu, x = 26;
		SinhVien sv;
		string text,field[6];
		field[0] = ""; // ma lop
		field[1] = ""; // ma sv
		field[2] = ""; // ho sv
		field[3] = ""; //  ten sv
		field[4] = ""; // phai
		field[5] = ""; // sdt
	
		// xóa form nhap sv
		for(int u=2;u<11;u+=2){
			gotoxy(boxx + x , boxy + u);
			cout <<"                              ";
		}
				
		for(int n = 2, k = 1; k < 7; k++, n+=2)
		{	
		 	if(k == 5){
			 	text = GioiTinh();
			 	if(text == "ESC"){
				 	if(demsv != 0){
						if(XacNhanLuuFile() == true)
						{
							SapXepSv(l);
							SaveData_Sv(l);
						}
						return;			
					}
					else{					
						Sleep(500);
						return;
					}			
				 }
				 else if(text == "TAB"){
				 	if(demsv != 0){
						if(XacNhanLuuFile() == true)
						{
							SapXepSv(l);
							SaveData_Sv(l);
						}	
					}
					else{
						Sleep(500);
					}
				 }		 
			 	Print_Textfield(text, n,x);
			 	field[k-1] = text;
			 	continue;		 	
		 	}
			keyhit = 0;
			demkitu = field[k-1].length();
			text = field[k-1];
			while(keyhit != ENTER)
			{
				gotoxy(boxx + x , boxy + n);
				if(demkitu < boxs - x)
				{
					cout << text; 
					for(int i = 1; i < boxs - (x - 1) - demkitu; i++) cout << " ";
				}
				else
				{
					for(int i = demkitu - boxs + x; i < demkitu; i++) cout << text[i];
				}
		
				gotoxy(boxx + x + demkitu , boxy + n); //Xuat ra vi tri con tro khi nhap
				keyhit = getch();
			
				if(keyhit == 224)
				 {
					keyhit = getch();
					if(keyhit == UP)
					 {			 	
						if(k == 2) text = Fix_Ma(text);
					 	if(k == 3 || k == 4) text = Fix_HoTen(text);
						Print_Textfield(text, n,x);
						field[k-1] = text;
						Del_Error();
						if(k > 1){
							n-=2;
							k--;						
						}
						if(k == 5){
							text = GioiTinh();
							if(text == "ESC"){
				 				if(demsv != 0){
									if(XacNhanLuuFile() == true)
									{
										SapXepSv(l);
										SaveData_Sv(l);
									}
									return;			
								}
								else{					
									Sleep(500);
									return;
								}
							}
							else if(text == "TAB"){
				 				if(demsv != 0){
										if(XacNhanLuuFile() == true)
										{
											SapXepSv(l);
											SaveData_Sv(l);
										}		
								}
								else{
									Sleep(500);
								}
							}				 	
						 	Print_Textfield(text, n,x);
			 				field[k-1] = text;	
		 				}
						demkitu = field[k-1].length();
						text = field[k-1];
					}
				}
				else if(keyhit == BACKSPACE)
				 {
					if(demkitu > 0)
					{
						demkitu--;
						text = text.substr(0, text.size() - 1);
						Del_Error();
					}
				}	
				else if((keyhit > 96 && keyhit < 123) || (keyhit > 64 && keyhit < 91) || (keyhit == 45)|| (keyhit == 32)|| (keyhit > 47 && keyhit < 58)) {		
					if((keyhit == 32) || (keyhit > 96 && keyhit < 123) || (keyhit > 64 && keyhit < 91) || (keyhit > 47 && keyhit < 58))
						if(k == 1 && text.length() < 30){
							demkitu++;
							text += char(keyhit);
							Del_Error();
						}	
					if((keyhit == 45) || (keyhit > 96 && keyhit < 123) || (keyhit > 64 && keyhit < 91) || (keyhit > 47 && keyhit < 58))
						if(k == 2 && text.length() < 11 ){
							demkitu++;
							text += char(keyhit);
							Del_Error();
						}	
					if((keyhit == 32) || (keyhit > 96 && keyhit < 123) || (keyhit > 64 && keyhit < 91))
						if(k == 3 && text.length() < 30){
							demkitu++;
							text += char(keyhit);
							Del_Error();
						}
					if((keyhit > 96 && keyhit < 123) || (keyhit > 64 && keyhit < 91))
						if(k == 4 && text.length() < 10){
							demkitu++;
							text += char(keyhit);
							Del_Error();
						}						
					if (keyhit > 47 && keyhit < 58)
						if(k == 6 && text.length() < 11){
							demkitu++;
							text += char(keyhit);
							Del_Error();
						}					
										
				}			
				else if(keyhit == ESC) break;
				else if(keyhit == TAB) break;

			}
			if(keyhit == ESC) {
				if(demsv != 0){
					if(XacNhanLuuFile() == true)
					{
						SapXepSv(l);
						SaveData_Sv(l);
					}						
				}
				else Sleep(500);			
				return;
			}
			else if(keyhit == TAB) {
				if(demsv != 0){				
					if(XacNhanLuuFile() == true)
					{
						SapXepSv(l);
						SaveData_Sv(l);
					}
				}
				else Sleep(500);			
			}
			if(k == 1) text = Fix_Ma(text);
			if(k == 2 || k==3) text = Fix_HoTen(text);					
			Print_Textfield(text,n,x);
			field[k-1] = text;
			Del_Error();
		}	
		if(keyhit == ENTER) {				
			strcpy(sv.MaLop, field[0].c_str());
			strcpy(sv.MaSv, field[1].c_str());
			strcpy(sv.Ho, field[2].c_str());
			strcpy(sv.Ten, field[3].c_str());
			strcpy(sv.Phai, field[4].c_str());
			strcpy(sv.SoDt, field[5].c_str());
			sv.First = NULL; // khoi tao ds diem
			ThemSvVaoSau(l,sv);
			demsv++;
			SapXepSv(l);
			SaveData_Sv(l);
			
			Sleep(500);
			goto NHAP;
		}
}

void Raw_Button_F1(int color_text, int color_fill)
{
	gotoxy(13,31); textcolor(color_fill); cout <<"        ";
	gotoxy(13,32); textcolor(color_text); cout <<"   F1   ";
	gotoxy(13,33); textcolor(color_fill); cout <<"        ";
	gotoxy(12,34); textcolor(59); cout <<"   THEM   ";
}
void Raw_Button_F5(int color_text, int color_fill)
{
	gotoxy(45,31); textcolor(color_text); cout <<"        ";
	gotoxy(45,32); textcolor(color_text); cout <<"   F5   ";
	gotoxy(45,33); textcolor(color_text); cout <<"        ";
	gotoxy(43,34); textcolor(59); cout <<"LUU VAO FILE";
}

void Raw_Button_ESC(int color_text, int color_fill)
{
	gotoxy(74,31); textcolor(color_text); cout <<"         ";
	gotoxy(74,32); textcolor(color_text); cout <<"   ESC   ";
	gotoxy(74,33); textcolor(color_text); cout <<"         ";
	gotoxy(70,34); textcolor(59); cout <<"TRO VE MENU CHINH";
}
void Raw_Button_OK(int color_text, int color_fill)
{
	gotoxy(15,31); textcolor(color_fill); cout <<"         ";
	gotoxy(15,32); textcolor(color_text); cout <<"  ENTER  ";
	gotoxy(15,33); textcolor(color_fill); cout <<"         ";
	gotoxy(15,34); textcolor(59); cout <<"   OK   ";
}

void Raw_Button_TAB(int color_text, int color_fill)
{
	gotoxy(15,31); textcolor(color_fill); cout <<"         ";
	gotoxy(15,32); textcolor(color_text); cout <<"   TAB   ";
	gotoxy(15,33); textcolor(color_fill); cout <<"         ";
	gotoxy(16,34); textcolor(59); cout <<" TRO VE";
}

void Raw_Button_YES(int color_text)
{
	int x=32,y=16;
	textcolor(color_text); gotoxy(x+5,y+5); cout <<"CO";
	gotoxy(x + 2, y+4); cout << char(201);
	for(int i = 1; i < 8; i++) cout << char(205);
	cout << char(187);
	gotoxy(x+2,y+5);cout << char(186);
	gotoxy(x+10,y+5);cout << char(186);
	gotoxy(x + 2, y+6); cout << char(200);
	for(int i = 1; i < 8; i++) cout << char(205);
	cout << char(188);
}

void Raw_Button_NO(int color_text)
{
	int x=29,y=16;
	textcolor(color_text); gotoxy(x+29,y+5); cout <<"KHONG";
	gotoxy(x + 27, y+4); cout << char(201);
	for(int i = 1; i < 8; i++) cout << char(205);
	cout << char(187);
	gotoxy(x+27,y+5);cout << char(186);
	gotoxy(x+35,y+5);cout << char(186);
	gotoxy(x + 27, y+6); cout << char(200);
	for(int i = 1; i < 8; i++) cout << char(205);
	cout << char(188);
}

void Frame_Mh(int x, int y)
{
	int n;
	gotoxy(x,y);      cout <<"    Nhap Vao Ma Mon Hoc    ";
	gotoxy(x,y+1);    cout <<" ";
	gotoxy(x+1,y+1);  cout <<"                         ";
	gotoxy(x+26,y+1); cout <<" ";
	gotoxy(x,y+2); ;  cout <<"                           "; 
}

int CheckTontai_Mh(ListMonHoc lmh)
{
	
	int x= 36, y = 17;	
	int keyhit,demkitu;
	string text,field;
	field = ""; // ma mon hoc
	char temp[11];

	Frame_Mh(x,y);

	keyhit = 0;
	demkitu = field.length();
	text = field;
	while(1)
	{	
		gotoxy(x + 8, y + 1);
		if(demkitu < 10 ){
			cout << text;
			for(int i = 1; i < 11 - demkitu; i++) cout << " ";// xoa duoi
		}
		else
		{
			for(int i = demkitu - 10 ; i < demkitu; i++) cout << text[i];
		}
		if(demkitu < 10) gotoxy(x + 8 + demkitu, y + 1); //Xuat ra vi tri con tro

		keyhit = getch();
		if(keyhit == 224)
		{
			keyhit = getch();
			if(keyhit == DOWN || keyhit == UP || keyhit == LEFT || keyhit == RIGHT|| keyhit == INS || keyhit == DEL || keyhit == HOME || keyhit == END || keyhit == PGU || keyhit == PGD)
				continue;
		}
		else if(keyhit == BACKSPACE)
		 {
			if(demkitu > 0)
			{
				demkitu--;
				text = text.substr(0, text.size() - 1);
				Del_Error();
			}
		}
		else if(demkitu < 10 && ((keyhit == 45) || (keyhit > 96 && keyhit < 123) || (keyhit > 64 && keyhit < 91) || (keyhit > 47 && keyhit < 58))){
				demkitu++;
				text += toupper(char(keyhit));
				Del_Error();
		}
		else if(keyhit == ENTER)
		{
			if(text == ""){
				Del_Error();
				keyhit = 0;
				gotoxy(boxx + 15 , 29);
				cout << " VUI LONG NHAP MA MON HOC DE KIEM TRA! ";
			}
			else
			 {
				Del_Error();					
				strcpy(temp, text.c_str());	
				if(CheckMaMh(lmh,temp) == -1){
					gotoxy(boxx + 15 , 29);
					cout << " MON HOC KHONG TON TAI! XIN KIEM TRA LAI! ";
				}
				else 
				{
					return CheckMaMh(lmh,temp); 
				}
			}
		}
		else if(keyhit == ESC)
		{
			return -2;
		}
		else if(keyhit == TAB)
		{
			return -1;
		}			
	}
}

string Fix_Diem(string t) {

	if(t != "") {              // t co ki tu
		if(atof(t.c_str()) == 0) return "0.00";
		if(t == "10") return "10.0";
		if(t[0] == '0', t[1] == '.');				
		else{
			while(t[0] == '0')
				t.erase(0, 1); //Xoa so 0 o dau
		}
		// them so 0
		if(t.length() == 1)
			t += ".00";
		else if(t.length() == 2)
			t += "00";
		else if(t.length() == 3)
			t += "0";		 	
	}
	return t;
}
void Print_Diem(string t, int k, int x, int y)
{
	gotoxy(tabx + x, taby + y + k);
	for(int i = 0; i < t.length(); i++) cout << t[i];
}

int DiemTheoLan(int x, int y)
{
	gotoxy(x,y);    cout <<"   Lan Nhap Diem   ";
	gotoxy(x-5,y+2); cout <<" LAN 1 ";
	gotoxy(x+17,y+2);cout <<" LAN 2 ";
	
	int keyhit,Lan;
	ListMonHoc lmh;
	Lan = 1;
	
	while(keyhit != ENTER)
	{
		keyhit = getch();
		if(keyhit == 224)
		{
			keyhit = getch();
			if(keyhit == LEFT){
				if(Lan == 1)
				{
					gotoxy(x-5,y+2);  cout <<" LAN 1 ";
					gotoxy(x+17,y+2); cout <<" LAN 2 ";
					Lan = 2;		
				}	
				else if(Lan == 2){
					gotoxy(x-5,y+2); cout <<" LAN 1 ";
					gotoxy(x+17,y+2); cout <<" LAN 2 ";
					Lan = 1;
				}
			}
			else if(keyhit == RIGHT){
				if(Lan == 2)
				{
					gotoxy(x-5,y+2);  cout <<" LAN 1 ";
					gotoxy(x+17,y+2);  cout <<" LAN 2 ";
					Lan = 1;							
				}	
				else if(Lan == 1){
					gotoxy(x-5,y+2);  cout <<" LAN 1 ";
					gotoxy(x+17,y+2);  cout <<" LAN 2 ";
					Lan = 2;
				}
			}					
		}
		if(keyhit == ESC){
			return -1;
		}
		if(keyhit == TAB){			
			return 0;
		}		
		else if(keyhit == ENTER)
		{		
			Sleep(500);
			return Lan;	
		}		
	}
}

void Box_XacnhanNhapLai()
{
	int x = 32, y = 17, h = 6, s = 34;
	
	// doi mau tab
	for(int j = 0;j<h;j++)
	for(int i = 0;i< s;i++){
		gotoxy(x+i,y + j);cout <<" ";
	}
	// khung
	gotoxy(x+4,y+1); cout <<"BAN CO MUON NHAP LAI KHONG?";
	gotoxy(x, y);	cout << char(218);
	for(int i = 1; i < s; i++) cout << char(196);
	cout << char(191);
	for(int i = 1; i < h; i++) 
	{
		gotoxy(x, y + i); cout << char(179);
	}
	
	for(int i = 1; i < h + 1; i++)
	{
	
		gotoxy(x + s, y + i); cout << char(179);
	}
		
	gotoxy(x, y + h); cout << char(192);
	
	for(int i = 1; i < s ; i++)
	{
		gotoxy(x + i, y + h); cout << char(196);
	}
	
	gotoxy(x + s,y+h); cout <<char(217);
	
	// nut co
	Raw_Button_YES(86);
	
	// nut khong
	Raw_Button_NO(86);
	
}
bool XacNhanNhapLai()
{
	int keyhit;
	Box_XacnhanNhapLai();
	Raw_Button_NO(7);
	bool Nhap = false;
	
	while(keyhit != ENTER){
		keyhit = getch();
		if(keyhit == 224)
		{
			keyhit = getch();
			if(keyhit == LEFT){
				if(Nhap == true)
				{
					Raw_Button_YES(86);
					Raw_Button_NO(7);
					Nhap = false;		
				}	
				else {
					Raw_Button_YES(7);
					Raw_Button_NO(86);
					Nhap = true;
				}
			}
			else if(keyhit == RIGHT){
				if(Nhap == false)
				{
					Raw_Button_YES(7);
					Raw_Button_NO(86);
					Nhap = true;							
				}	
				else {
					Raw_Button_YES(86);
					Raw_Button_NO(7);
					Nhap = false;
				}
			}					
		}
		if(keyhit == ENTER)
		{
			return Nhap;
		}		
	}
}

/*void NhapDiem(ListLopTinChi &llh, ListMonHoc lmh)
{
CHECKLOP:
	system("cls");
	 
	int vitrilop,vitrimon;
	LoadData_Lop(llh);
//	LoadData_Sv(llh);
	LoadData_Diem(llh);
	gotoxy(1,1);cout <<"   NHAP DIEM    	";
	
	if(vitrilop == -1){
		Sleep(500);
		return;
	}
	
	if(llh.LLTC[vitrilop].First == NULL)
	{

		gotoxy(boxx + 10 , 29); cout << " DANH SACH SINH VIEN LOP            HIEN CHUA CO!";
	    gotoxy(boxx + 35 , 29); cout<<llh.LLTC[vitrilop].MaLop;
		int key;
		while(1){
			key = getch();
			if(key == TAB || key == ENTER)
			{
				goto CHECKLOP;
			}
			else if(key == ESC)
			{
				Sleep(500);
				return;
			}
		}
	}
	
	gotoxy(36,13); cout <<"      XAC NHAN MA LOP      ";
	Sleep(500);
	vitrimon = CheckTontai_Mh(lmh);
		
	if(vitrimon == -1){
		Sleep(500);	
		goto CHECKLOP;
	}
	else if(vitrimon == -2){
		Sleep(500);
		return;
	}
	
	gotoxy(36,17);cout <<"    XAC NHAN MA MON HOC    ";	
	Sleep(500);
	
CHECKLAN:
	int DiemLan = DiemTheoLan(40, 21);
	char temp[2];
	Diem lan1, lan2, lan;
	//fix mau
	gotoxy(36,13);  cout <<"      XAC NHAN MA LOP      ";
	gotoxy(36,14);    cout <<" ";
	gotoxy(36+1,14);  cout <<"                         ";
	gotoxy(36+26,14);   cout <<" ";
	gotoxy(36,15);    cout <<"                           ";
	gotoxy(44,14);  cout << llh.LLTC[vitrilop].MaLop;
	
	gotoxy(36,17); cout <<"    XAC NHAN MA MON HOC    ";
	gotoxy(36,18);  cout <<" ";
	gotoxy(36+1,18); cout <<"                         ";
	gotoxy(36+26,18); cout <<" ";
	gotoxy(36,19);  cout <<"                           ";
//	gotoxy(44,18); cout << lmh.DanhSachMon[vitrimon].MaMh;

	if(DiemLan == 0){
		Sleep(500);
		goto CHECKLOP;
	}	
	else if(DiemLan == -1){
		Sleep(500);
		return;
	}
	
//	strcpy(lan1.MaMh,lmh.DanhSachMon[vitrimon].MaMh);
	strcpy(lan1.Lan, itoa(1,temp,10));
//	strcpy(lan2.MaMh,lmh.DanhSachMon[vitrimon].MaMh);
	strcpy(lan2.Lan, itoa(2,temp,10));
	
	if(DiemLan == 2) // check diem lan 1 da ton tai chua
	{					
		if ( DuyetDanhSachDiem(llh.LLTC[vitrilop].First->info.First,lan1) == -1)
		{
		
			gotoxy(boxx + 10 , 29); cout << " DANH SACH DIEM LAN 1 CUA MON ";
//			gotoxy(boxx + 40 , 29); cout << lmh.DanhSachMon[vitrimon].MaMh <<" CHUA TON TAI! ";
			goto CHECKLAN;
		}
	}
	
//	strcpy(lan.MaMh,lmh.DanhSachMon[vitrimon].MaMh);
	strcpy(lan.Lan, itoa(DiemLan,temp,10));

	// kiem tra diem da nhap chua
	if ( DuyetDanhSachDiem(llh.LLTC[vitrilop].First->info.First,lan) != -1)
	{
		if(XacNhanNhapLai() == true)
		{		
			if(atoi(lan.Lan) == 1)
			{
				XoaDiem(llh.LLTC[vitrilop].First, lan1);
				XoaDiem(llh.LLTC[vitrilop].First, lan2);
			}
			else
			{
				XoaDiem(llh.LLTC[vitrilop].First, lan);
			}		
		}
		else
		{
			goto CHECKLOP;
		}
	}

	int TongSv,TongTrang, TrangHienTai = 0, SoSv = 0;
	
	//SapXepSv(llh.LLTC[vitrilop].First);	
	TongSv = 100; //TongSoSinhVien(llh.LLTC[vitrilop].First);
	SinhVien sv[TongSv];
	
	for(ListSvPtr p = llh.LLTC[vitrilop].First; p != NULL; p = p->next)
	{
		if(DiemLan == 2) // sv co diem < 5 moi dc nhap diem lan 2
		{	
			for(ListDiemPtr q = p->info.First; q != NULL; q = q->next)
			{
//				if(strcmp(lmh.DanhSachMon[vitrimon].MaMh, q->data.MaMh) == 0)
				{					
					if(atoi(q->data.Lan) == 1 && atof(q->data.diem) < 5)
					{
						sv[SoSv] = p->info;
						SoSv++;
						break;
					}
				}
			}
		}
		else
		{
			sv[SoSv] = p->info;
			SoSv++;		
		}
	}
	TongTrang = ((SoSv+1)/21) + 1;
	
	system("cls");	
	 
		
	//Nhap diem
	int keyhit, x = 76, y = 2,check = 0;
	int demkitu;
	string text, field[TongSv];
	char Lan[2];
	itoa(DiemLan,Lan,10);
	
	Diem d[TongSv];
	
	for(int i = 0; i < TongSv; i++)
	{
		field[i] = ""; // diem
	}
	
	int i = 0;
	for(ListSvPtr p = llh.LLTC[vitrilop].First; p != NULL; p = p->next)
	{
		if(llh.LLTC[vitrilop].First->info.First == NULL)
		{
			p->info.First = NULL;
		}
		strcpy(d[i].diem,field[i].c_str());
		strcpy(d[i].Lan, Lan);
//		strcpy(d[i].MaMh, lmh.DanhSachMon[vitrimon].MaMh);
		ThemDiemVaoDau(p->info.First,d[i]);
		i++;
	}
	
	for(int k = 1, n = 1, t = 0; k <= SoSv; k++,n++)
	{
XUATSV:
		gotoxy(20,2); textcolor(91); cout <<" NHAP DIEM MON ";
//		gotoxy(35,2); textcolor(94); cout << strupr(lmh.DanhSachMon[vitrimon].TenMh);
//		gotoxy(35 + strlen(lmh.DanhSachMon[vitrimon].TenMh),2); textcolor(91);
//		gotoxy(44 + strlen(lmh.DanhSachMon[vitrimon].TenMh),2); textcolor(94);	
		TableDiemNhap(DiemLan);
		
		if(DiemLan == 2)
		{
			gotoxy(21,3); cout << " LUU Y: CHI NHUNG SINH VIEN DIEM DUOI 5.00 MOI DUOC THI LAI";
		}
		gotoxy(tabx + 38, taby + tabh + 1); cout <<"Trang "<< TrangHienTai + 1<<"/"<<TongTrang;
		if(TrangHienTai > 0){
			gotoxy(tabx + 58, taby + tabh + 1); cout<<"["<<(char)ArrowUP<<"]"<<" Tro Lai |";
		}
					
		for(int i = TrangHienTai * 20,j = 3,z = 1; i < 20 + TrangHienTai* 20 && i < SoSv; i++,j++,z++)
		{
		
			gotoxy(tabx + 2, taby + j); cout << i+1;
			gotoxy(tabx +9, taby + j); cout << sv[i].MaSv;
			gotoxy(tabx +25, taby + j); cout << sv[i].Ho;
			gotoxy(tabx +55, taby + j); cout << sv[i].Ten;
			if(z % 20 == 1)
			{
				z = 1;
			}
			Print_Diem(field[i],z,x,y);
		}
	
		check = 0;
		keyhit = 0;
		demkitu = field[k-1].length();
		text = field[k-1];
		while(keyhit != ENTER)
		{
		 	check = 0;
		 	for(int i = 0; i<text.length(); i++){
				if(text[i] == '.'){
					check = 1;
					break;
				}
			}
						
			gotoxy(tabx + x ,  taby + y + n); // xuat vi tri dong` nhap
			
			if(demkitu < 4)
			 {
				cout << text;
				for(int i = 1; i < 5 - demkitu; i++) cout << " ";
			 }
			else
			{
				for(int i = demkitu - 4; i < demkitu; i++) cout << text[i];
			}
	
			gotoxy(tabx + x + demkitu , taby + y + n); //Xuat ra vi tri con tro khi nhap
			keyhit = getch();
			
			if(keyhit == 224)
			{
				keyhit = getch();
				if(keyhit == UP)
				{										
					if(atof(text.c_str()) > 10)
					{
						keyhit = 0;
						gotoxy(boxx +10, 29); cout << " CANH BAO: DIEM KHONG HOP LE! XIN KIEM TRA LAI! ";				
						Print_Diem(text,n,x,y);
					}
					else
					{
						if(n % 20 == 1){
							if(TongTrang > 1 && TrangHienTai > 0)
							{
								TrangHienTai--;
								text = Fix_Diem(text);				
								Print_Diem(text,n,x,y);
								field[k-1] = text;
								Del_Error();								
								
								// luu diem theo ma
								for(ListSvPtr u = llh.LLTC[vitrilop].First; u != NULL; u = u->next)
								{			
									if(strcmp(sv[k-1].MaSv, u->info.MaSv) == 0)
									{
										strcpy(d[k-1].diem,field[k-1].c_str());
										XoaDiemDau(u->info.First);
										ThemDiemVaoDau(u->info.First,d[k-1]);
										break;
									}
								}
								
								if(k > 1)
								{
									t--;
									n=20;
									k--;
								
									system("cls");
									goto XUATSV;
								}
							}
						}							
						text = Fix_Diem(text);				
						Print_Diem(text,n,x,y);
						field[k-1] = text;
						Del_Error();
						
						// luu diem theo ma
						for(ListSvPtr u = llh.LLTC[vitrilop].First; u != NULL; u = u->next)
						{			
							if(strcmp(sv[k-1].MaSv, u->info.MaSv) == 0)
							{
								strcpy(d[k-1].diem,field[k-1].c_str());
								XoaDiemDau(u->info.First);
								ThemDiemVaoDau(u->info.First,d[k-1]);
								break;			
							}
						}
												
						if(k > 1)
						{
							n--;
							k--;
						
						}
						demkitu = field[k-1].length();
						text = field[k-1];
					}							
				}
				else if(keyhit == DOWN)
				{			
					if(atof(text.c_str()) > 10) // neu diem ko hop le
					{
						keyhit = 0;
						textcolor(206); gotoxy(boxx +10, 29); cout << " CANH BAO: DIEM KHONG HOP LE! XIN KIEM TRA LAI! ";				
						Print_Diem(text,n,x,y);
					}
					else // diem hop le
					{
						if(n % 20 == 0)
						{ // check vi tri cua n
							if(TongTrang > 1 && TrangHienTai + 1 < TongTrang)
							{
								TrangHienTai++;
								text = Fix_Diem(text);				
								Print_Diem(text,n,x,y);
								field[k-1] = text;
								Del_Error();
								
								// luu diem theo ma
								for(ListSvPtr u = llh.LLTC[vitrilop].First; u != NULL; u = u->next)
								{			
									if(strcmp(sv[k-1].MaSv, u->info.MaSv) == 0)
									{
										strcpy(d[k-1].diem,field[k-1].c_str());
										XoaDiemDau(u->info.First);
										ThemDiemVaoDau(u->info.First,d[k-1]);
										break;
									}
								}
								
								if(k < SoSv)
								{						
									n=1;
									k++;
									
									system("cls");
									goto XUATSV;
								}													
							}
						}																			
						text = Fix_Diem(text);				
						Print_Diem(text,n,x,y);
						field[k-1] = text;
						Del_Error();
						
						// luu diem theo ma
						for(ListSvPtr u = llh.LLTC[vitrilop].First; u != NULL; u = u->next)
						{			
							if(strcmp(sv[k-1].MaSv, u->info.MaSv) == 0)
							{
								strcpy(d[k-1].diem,field[k-1].c_str());
								XoaDiemDau(u->info.First);
								ThemDiemVaoDau(u->info.First,d[k-1]);
								break;
							}
						}
										
						if(k < SoSv){
							n++;
							k++;
							
						}
						demkitu = field[k-1].length();
						text = field[k-1];
					}				
				}
			}
			else if(keyhit == BACKSPACE)
			{
				if(demkitu > 0)
				{
					demkitu--;
					text = text.substr(0, text.size() - 1);
					Del_Error();
				}
			}
			else if(demkitu < 4 && ((keyhit > 47 && keyhit < 58) || (keyhit == 46)))
			{
				if(demkitu == 0 && keyhit == 46) continue; // ki tu dau la dau. thi bo qua
				if(keyhit == 46) if(check == 1) continue; // kiem tra da ton tai dau . chua
			
				demkitu++;
				text += char(keyhit);
				
				if((demkitu == 1) && (keyhit != 49)){ // ki tu dau khac so 1 thi them dau phay
					demkitu++;
					text += char(46);
				}
				else if((demkitu == 2) && (keyhit != 46))
				{
					demkitu++;
					text += char(46);
				}
				Del_Error();
			}
			else if(keyhit == ENTER)
			{
				if(atof(text.c_str()) > 10)
				{
					keyhit = 0;
					gotoxy(boxx + 10, 29); cout << " CANH BAO: DIEM KHONG HOP LE! XIN KIEM TRA LAI! ";
					Print_Diem(text,n,x,y);
				}
			}
			else if(keyhit == ESC) break;
			else if(keyhit == TAB) break;	
		}
		if(keyhit == ESC)
		{					
			if(XacNhanLuuFile() == true)
			{
				SaveData_Diem(llh);
			}
			else Sleep(500);
			return;
		}
		else if(keyhit == TAB)
		{				
			if(XacNhanLuuFile() == true)
			{
				SaveData_Diem(llh);
			}		
			else Sleep(500);
			goto CHECKLOP;
		}				
		if(n % 20 == 0)
		{
			if(TongTrang > 1 && TrangHienTai + 1 < TongTrang)
			{
				TrangHienTai++;
				text = Fix_Diem(text);		
				Print_Diem(text,n,x,y);
				field[k-1] = text;
				Del_Error();
				
				for(ListSvPtr u = llh.LLTC[vitrilop].First; u != NULL; u = u->next)
				{			
					if(strcmp(sv[k-1].MaSv, u->info.MaSv) == 0)
					{
						strcpy(d[k-1].diem,field[k-1].c_str());
						XoaDiemDau(u->info.First);
						ThemDiemVaoDau(u->info.First,d[k-1]);
						break;
					}
				}
		
				system("cls");
				n=1;
				k++;
				goto XUATSV;;
			}
		}
		text = Fix_Diem(text);				
		Print_Diem(text,n,x,y);
		field[k-1] = text;
		Del_Error();
		
		for(ListSvPtr u = llh.LLTC[vitrilop].First; u != NULL; u = u->next)
		{			
			if(strcmp(sv[k-1].MaSv, u->info.MaSv) == 0)
			{
				strcpy(d[k-1].diem,field[k-1].c_str());
				XoaDiemDau(u->info.First);
				ThemDiemVaoDau(u->info.First,d[k-1]);
				break;
			}
		}
	}
	if(keyhit == ENTER)
	{
		if(XacNhanLuuFile() == true)
		{
			SaveData_Diem(llh);
		}		
		else Sleep(500);
	}
}*/

void Frame_Nienkhoa(int x, int y)
{
	int n;
	gotoxy(x,y);    cout <<"    Nhap Vao Nien Khoa    ";
	gotoxy(x,y+1);  cout <<" ";
	gotoxy(x+1,y+1); cout <<"                        ";
	gotoxy(x+25,y+1);  cout <<" ";
	gotoxy(x,y+2);  cout <<"                          "; 
}

int CheckNienKhoa(ListLopTinChi llh, char nk[])
{
	for(int i = 0; i < llh.n ; i++)
		{
			if (strcmp(llh.LLTC[i].NienKhoa, nk)== 0)
			{
				return 1;
			}
		}
		return 0;
}

string CheckNienKhoa(ListLopTinChi llh)
{
	
	int x= 36, y = 14;	
	int keyhit,demkitu;
	string text,field;
	field = ""; // nien khoa
	char temp[5];

	Frame_Nienkhoa(x,y);

	keyhit = 0;
	demkitu = field.length();
	text = field;
	while(1)
	{	
		gotoxy(x + 10, y + 1);
		if(demkitu < 4 ){
			cout << text;
			for(int i = 1; i < 5 - demkitu; i++) cout << " ";// xoa duoi
		}
		else
		{
			for(int i = demkitu - 4 ; i < demkitu; i++) cout << text[i];
		}
		if(demkitu < 4) gotoxy(x + 10 + demkitu, y + 1); //Xuat ra vi tri con tro

		keyhit = getch();
		
		 if(keyhit == BACKSPACE)
		 {
			if(demkitu > 0)
			{
				demkitu--;
				text = text.substr(0, text.size() - 1);
				Del_Error();
			}
		}
		else if(demkitu < 4 && (keyhit > 47 && keyhit < 58)){
				demkitu++;
				text += char(keyhit);
				Del_Error();
		}
		else if(keyhit == ENTER)
		{
			if(text == ""){
				Del_Error();
				keyhit = 0;
				gotoxy(boxx + 15 , 29);
				cout << " VUI LONG NHAP NIEN KHOA DE KIEM TRA! ";
			}
			else {							
				Del_Error();
				if(text.length() < 4) {							
					gotoxy(boxx + 10 , 29);
					cout << " NIEN KHOA KHONG HOP LE! XIN KIEM TRA LAI! ";
				}
				else{
					strcpy(temp, text.c_str());	
					if(CheckNienKhoa(llh,temp) == 0){
						gotoxy(boxx + 5 , 29);
						cout << " KHONG CO LOP NAO TRONG NIEN KHOA      ! XIN KIEM TRA LAI! ";
						gotoxy(boxx + 39 , 29); cout << text;
					}
					else {
						return temp;
					}
				}
			}
		}
		else if(keyhit == ESC)
		{
			return "0";
		}		
	}
}

void XuatLopTheoNienKhoa(ListLopTinChi &llh)
{
NHAPNK:
	system("cls");
	 
	
	LoadData_Lop(llh);
//	LoadData_Sv(llh);
	string nienkhoa;

	textcolor(62);	
	gotoxy(10,1);cout <<"    ____              __       _____            __       __              ";
	gotoxy(10,2);cout <<"   / __ \\____ _____  / /_     / ___/____ ______/ /_     / /   ____  ____ ";
	gotoxy(10,3);cout <<"  / / / / __ `/ __ \\/ __ \\    \\__ \\/ __ `/ ___/ __ \\   / /   / __ \\/ __ \\";
	gotoxy(10,4);cout <<" / /_/ / /_/ / / / / / / /   ___/ / /_/ / /__/ / / /  / /___/ /_/ / /_/ /";
	gotoxy(10,5);cout <<"/_____/\\__,_/_/ /_/_/ /_/   /____/\\__,_/\\___/_/ /_/  /_____/\\____/ .___/ ";
	gotoxy(10,6);cout <<"                                                                /_/      ";
	
	gotoxy(15,7); cout <<"  ________                  _   ___               __ __ __               ";
	gotoxy(15,8); cout <<" /_  __/ /_  ___  ____     / | / (_)__  ____     / //_// /_  ____  ____ _";
	gotoxy(15,9);  cout<<"  / / / __ \\/ _ \\/ __ \\   /  |/ / / _ \\/ __ \\   / ,<  / __ \\/ __ \\/ __ `/";
	gotoxy(15,10);cout <<" / / / / / /  __/ /_/ /  / /|  / /  __/ / / /  / /| |/ / / / /_/ / /_/ / ";
	gotoxy(15,11);cout <<"/_/ /_/ /_/\\___/\\____/  /_/ |_/_/\\___/_/ /_/  /_/ |_/_/ /_/\\____/\\__,_/  ";
	
	
CHECKNK:
	nienkhoa = CheckNienKhoa(llh);
	
	if(nienkhoa == "0"){
		Sleep(500);
		return;
	}
	
	gotoxy(36,14); textcolor(185); cout <<"    XAC NHAN NIEN KHOA    ";
	Sleep(500);
	
	system("cls");
	
	int check = 0;

	int TongLop = 0, TongTrang, TrangHienTai = 0;
	SapXepLopHoc(llh);
	LopTinChi lh[llh.n];
			
	for(int i = 0; i < llh.n; i++)
	{
		if (strcmp(llh.LLTC[i].NienKhoa, nienkhoa.c_str()) == 0)
		{
			strcpy(lh[TongLop].MaMh, llh.LLTC[i].MaMh);
			strcpy(lh[TongLop].NienKhoa, llh.LLTC[i].NienKhoa);
			strcpy(lh[TongLop].HocKy, llh.LLTC[i].HocKy);
			strcpy(lh[TongLop].Nhom, llh.LLTC[i].Nhom);
			strcpy(lh[TongLop].SvMin, llh.LLTC[i].SvMax);
			TongLop++;
		}
	}

	TongTrang = (TongLop/21) + 1;
	
	int keyhit;
	char checkdel[10];
	
	while(1)
	{					
		system("cls");

		TableLop();
		gotoxy(30,2); cout <<" DANH SACH CAC LOP THUOC NIEN KHOA      ";
		gotoxy(65,2); cout <<nienkhoa;
	
		gotoxy(tabx + 38, taby + tabh + 1); cout <<"Trang "<< TrangHienTai + 1<<"/"<<TongTrang;
		if(TrangHienTai > 0){
			gotoxy(tabx + 58, taby + tabh + 1); cout<<"["<<(char)ArrowUP<<"]"<<" Tro Lai |";
		}
		for(int i = TrangHienTai * 20, j = 3; i < 20 + TrangHienTai * 20 && i <TongLop; i++, j++)
		{
			gotoxy(tabx +3 , taby + j); cout << i+1;
			gotoxy(tabx +5, taby + j); cout << lh[i].MaMh;
			gotoxy(tabx +45, taby + j); cout << lh[i].NienKhoa;
			gotoxy(tabx +55, taby + j); cout << lh[i].HocKy;
			gotoxy(tabx +65, taby + j); cout << lh[i].Nhom;
			gotoxy(tabx +75, taby + j); cout << lh[i].SvMin;
			gotoxy(tabx +85, taby + j); cout << lh[i].SvMax;
		}
		keyhit = getch();
		
		if(keyhit == 224)
		{
			keyhit = getch();
			if(keyhit == UP){												
				if(TongTrang > 1 && TrangHienTai > 0)
				{
					TrangHienTai--;
					continue;
				}
			}												
			else if(keyhit == DOWN){					
				if(TongTrang > 1 && TrangHienTai + 1 < TongTrang)
				{
					TrangHienTai++;
					system("cls");
					continue;
				}														
			}			

		}
		else if(keyhit == ESC)
		{
			if (check != 0){
				if(XacNhanLuuFile() == true)
				{
					SaveData_LopTC(llh);
				}
			}
			else Sleep(500);
			return;	
		}
		else if(keyhit == TAB)
		{
			if (check != 0){
				if(XacNhanLuuFile() == true)
				{	
					SaveData_LopTC(llh);	
				}
			}
			else Sleep(500);
			goto NHAPNK;
		}				
	}		
}

/*void XuatMonHoc(ListLopTinChi &llh,ListMonHoc &lmh)
{
	system("cls");
	 
	int TongMon = lmh.n, TongTrang, TrangHienTai = 0;	
	MonHoc mh[TongMon];
			
	TongTrang = (TongMon/21) + 1;	
	int keyhit;
	
	while(1)
	{					
		system("cls");
		 
		TableMon();	
		gotoxy(tabx + 38, taby + tabh + 1); cout <<"Trang "<< TrangHienTai + 1<<"/"<<TongTrang;
		if(TrangHienTai > 0){
			gotoxy(tabx + 58, taby + tabh + 1); cout<<"["<<(char)ArrowUP<<"]"<<" Tro Lai |";
		}	
		for(int i = TrangHienTai * 20, j = 3; i < 20 + TrangHienTai * 20 && i <TongMon; i++, j++)
		{
			gotoxy(tabx +3 , taby + j); cout << i+1;
			gotoxy(tabx +10, taby + j); cout << lmh.DanhSachMon[i].MaMh;
			gotoxy(tabx +29, taby + j); cout << lmh.DanhSachMon[i].TenMh;
			gotoxy(tabx +67, taby + j); cout << lmh.DanhSachMon[i].LT;
			gotoxy(tabx +78, taby + j); cout << lmh.DanhSachMon[i].TH;
		}
		keyhit = getch();
		
		if(keyhit == 224)
		{
			keyhit = getch();
			if(keyhit == UP)
			{												
				if(TongTrang > 1 && TrangHienTai > 0)
				{
					TrangHienTai--;
					continue;
				}
			}												
			else if(keyhit == DOWN)
			{					
				if(TongTrang > 1 && TrangHienTai + 1 < TongTrang)
				{
					TrangHienTai++;
					system("cls");
					continue;
				}														
			}
		}
		else if(keyhit == ESC)
		{
			Sleep(500);
			return;	
		}			
	}
}*/


void XuatThongTinSv(ListSv l,ListLopTinChi llh)
{
CHECKLOP:
	system("cls");
	LoadData_Lop(llh);
	LoadData_Sv(l);
	int vitrilop;
	int check=0;	
	//CheckTontai_Lop(l);
	char XetMaLop[15] ;
	strcpy(XetMaLop,CheckTontai_Lop(l));
RELOAD:	
	int TongSv = TongSoSinhVien(l), i=0, TongTrang, TrangHienTai =0;
	SinhVien sv[TongSv];
	TongSv = 0;
	for(ListSvPtr p = l.head ; p!= NULL; p = p->next){
		if(strcmp(XetMaLop,p->info.MaLop)==0){
			sv[i] = p->info;
			i++;
			TongSv++;
		}
	}
	TongTrang = (TongSv /21) + 1;	
	int keyhit;	
	while(1)
	{					
		system("cls");
		TableSv();
		gotoxy(20,2);
		cout <<" DANH SACH SINH VIEN CUA LOP "<<XetMaLop;
		gotoxy(tabx + 38, taby + tabh + 1); cout <<"Trang "<< TrangHienTai + 1<<"/"<<TongTrang;
		if(TrangHienTai > 0){
			gotoxy(tabx + 58, taby + tabh + 1); cout<<"["<<(char)ArrowUP<<"]"<<" Tro Lai |";
		}
		for(int i = TrangHienTai * 20, j = 3; i < 20 + TrangHienTai * 20 && i <TongSv; i++, j++)
		{
			gotoxy(tabx + 2, taby + j); cout << i+1;
			gotoxy(tabx +8, taby + j); cout << sv[i].MaSv;
			gotoxy(tabx +22, taby + j); cout << sv[i].Ho;
			gotoxy(tabx +45, taby + j); cout << sv[i].Ten;
			gotoxy(tabx +60, taby + j); cout << sv[i].Phai;
			gotoxy(tabx +65, taby + j); cout << sv[i].SoDt;
		}
		keyhit = getch();
		
		if(keyhit == 224)
		{
			keyhit = getch();
			if(keyhit == UP){												
				if(TongTrang > 1 && TrangHienTai > 0)
				{
					TrangHienTai--;
					system("cls");
					continue;
				}
			}												
			else if(keyhit == DOWN){					
				if(TongTrang > 1 && TrangHienTai + 1 < TongTrang)
				{
					TrangHienTai++;
					system("cls");
					continue;
				}														
			}	
		}
		else if(keyhit == ESC)
		{
			Sleep(500);
			return;	
		}
		else if(keyhit == TAB)
		{
			if (check != 0){
				if(XacNhanLuuFile() == true)
				{			
					SaveData_Sv(l);
					
				}
			}
			Sleep(500);
			goto CHECKLOP;
		}				
	}
}

/*void XuatDiemTheoLanThi(ListLopTinChi llh, ListMonHoc lmh)
{
CHECKLOP:
	system("cls");
	 
	int vitrilop,vitrimon;
	LoadData_Lop(llh);
//	LoadData_Sv(llh);
	LoadData_Diem(llh);

	textcolor(62);	
	gotoxy(2,1);cout <<"   _  __                  ____  _                   _____ _       __       _    ___          ";
	gotoxy(2,2);cout <<"  | |/ /__  ____ ___     / __ \\(_)__  ____ ___     / ___/(_)___  / /_     | |  / (_)__  ____ ";
	gotoxy(2,3);cout <<"  |   / _ \\/ __ `__ \\   / / / / / _ \\/ __ `__ \\    \\__ \\/ / __ \\/ __ \\    | | / / / _ \\/ __ \\";
	gotoxy(2,4);cout <<" /   /  __/ / / / / /  / /_/ / /  __/ / / / / /   ___/ / / / / / / / /    | |/ / /  __/ / / /";
	gotoxy(2,5);cout <<"/_/|_\\___/_/ /_/ /_/  /_____/_/\\___/_/ /_/ /_/   /____/_/_/ /_/_/ /_/     |___/_/\\___/_/ /_/ ";
	
	gotoxy(25,6); cout <<"  ________                  __              ";
	gotoxy(25,7); cout <<" /_  __/ /_  ___  ____     / /   ____  ____ ";
	gotoxy(25,8); cout <<"  / / / __ \\/ _ \\/ __ \\   / /   / __ \\/ __ \\";
	gotoxy(25,9);cout <<" / / / / / /  __/ /_/ /  / /___/ /_/ / /_/ /";
	gotoxy(25,10);cout <<"/_/ /_/ /_/\\___/\\____/  /_____/\\____/ .___/ ";
	gotoxy(25,11);cout <<"                                   /_/      ";
	
	
	if(vitrilop == -1){
		Sleep(500);
		return;
	}
	
	if(llh.LLTC[vitrilop].First == NULL)
	{
		gotoxy(boxx + 10 , 29); cout << " DANH SACH SINH VIEN LOP            HIEN CHUA CO! ";
		gotoxy(boxx + 35 , 29); cout<<llh.LLTC[vitrilop].MaLop;
		int key;
		while(1){
			key = getch();
			if(key == TAB || key == ENTER)
			{
				goto CHECKLOP;
			}
			else if(key == ESC)
			{
				Sleep(500);
				return;
			}
		}
	}
	
	gotoxy(36,13); cout <<"      XAC NHAN MA LOP      ";
	Raw_Button_OK(color_text2, color_fill2);
	Sleep(500);
	Raw_Button_OK(color_text1, color_fill1);
	Raw_Button_TAB(color_text1, color_fill1);
	
	vitrimon = CheckTontai_Mh(lmh);
		
	if(vitrimon == -1){
		Raw_Button_TAB(color_text2, color_fill2);
		Sleep(500);	
		goto CHECKLOP;
	}
	else if(vitrimon == -2){
		Raw_Button_ESC(color_text2, color_fill2);
		Sleep(500);
		return;
	}
	
	gotoxy(36,17); textcolor(185); cout <<"    XAC NHAN MA MON HOC    ";
	Raw_Button_OK(color_text2, color_fill2);	
	Sleep(500);
	Raw_Button_OK(color_text1, color_fill1);
	
	system("cls");
	 	
	
	int check = 0;
	
	
	int TongSv = 100;//TongSoSinhVien(llh.LLTC[vitrilop].First);
	int TongTrang, TrangHienTai = 0, i = 0;
	
	//SapXepSv(llh.LLTC[vitrilop].First);
	
	SinhVien sv[TongSv];
	for(ListSvPtr p = llh.LLTC[vitrilop].First; p != NULL; p = p->next)
	{
		sv[i] = p->info;
		i++;
	}	
		
	TongTrang = (TongSv /21) + 1;
	
	int keyhit;
	
	while(1)
	{					
		system("cls");
		 
		Raw_Button_ESC(color_text1,color_fill1);
		Raw_Button_TAB(color_text1,color_fill1);
		TableDiem2Lan();
		
		gotoxy(20,2);
		gotoxy(21,2); textcolor(91); cout <<" XEM DIEM MON ";
		gotoxy(35,2); textcolor(94); cout << strupr(lmh.DanhSachMon[vitrimon].TenMh);
		gotoxy(35 + strlen(lmh.DanhSachMon[vitrimon].TenMh),2); textcolor(91); cout <<" CUA LOP ";
		gotoxy(44 + strlen(lmh.DanhSachMon[vitrimon].TenMh),2); textcolor(94); 
	
		textcolor(59);
		gotoxy(tabx + 38, taby + tabh + 1); cout <<"Trang "<< TrangHienTai + 1<<"/"<<TongTrang;
		if(TrangHienTai > 0){
			gotoxy(tabx + 58, taby + tabh + 1); cout<<"["<<(char)ArrowUP<<"]"<<" Tro Lai |";
		}
		textcolor(62);	
		for(int i = TrangHienTai * 20, j = 3; i < 20 + TrangHienTai * 20 && i <TongSv; i++, j++)
		{
			gotoxy(tabx + 2, taby + j); cout << i+1;
			gotoxy(tabx +8, taby + j); cout << sv[i].Ho;
			gotoxy(tabx +38, taby + j); cout << sv[i].Ten;
			gotoxy(tabx+ 63, taby +j); cout << TruyXuatDiem(sv[i].First,lmh.DanhSachMon[vitrimon].MaMh, "1");
			gotoxy(tabx+ 76, taby +j); cout << TruyXuatDiem(sv[i].First,lmh.DanhSachMon[vitrimon].MaMh, "2");
		}
		keyhit = getch();
		
		if(keyhit == 224)
		{
			keyhit = getch();
			if(keyhit == UP){												
				if(TongTrang > 1 && TrangHienTai > 0)
				{
					TrangHienTai--;
					system("cls");
					continue;
				}
			}												
			else if(keyhit == DOWN){					
				if(TongTrang > 1 && TrangHienTai + 1 < TongTrang)
				{
					TrangHienTai++;
					system("cls");
					continue;
				}														
			}	
		}
		else if(keyhit == ESC)
		{
			Raw_Button_ESC(color_text2,color_fill2);
			Sleep(500);
			return;
		}
		else if(keyhit == TAB)
		{
			Raw_Button_TAB(color_text2,color_fill2);
			Sleep(500);
			goto CHECKLOP;
		}
	}
}*/

void TableDiemTB()
{
	gotoxy(tabx, taby);	cout << char(218);
	for(int i = 1; i < tabs + 1; i++) cout << char(196);
	cout << char(191);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx, taby + i); cout << char(179);
	}
	
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + tabs + 1, taby + i); cout << char(179);
	}		
	gotoxy(tabx, taby + tabh); cout << char(192);
	for(int i = 1; i < tabs + 1; i++)
	{
		gotoxy(tabx + i, taby + tabh); cout << char(196);
	}
	gotoxy(tabx + tabs+1,taby +tabh); cout <<char(217);
	gotoxy(tabx + 71, taby + tabh + 1);	cout<<" Tiep Theo ["<<(char)ArrowDOWN<<"]";
	//--------------------------------------
	//STT
	gotoxy(tabx +2, taby + 1); cout <<"STT";
	gotoxy(tabx + 6, taby); cout << char(194);	
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 6, taby + i); cout << char(179);
	}	
	gotoxy(tabx, taby +2); cout << char(195);	
	
	for(int i = 1; i < tabs + 1; i++) 
	{
		gotoxy(tabx +i, taby +2); cout << char(196);
	}
	gotoxy(tabx+ 6, taby +2); cout << char(197);
	gotoxy(tabx+ tabs +1, taby +2); cout << char(180);
	gotoxy(tabx+ 6, taby + tabh); cout << char(193);
	// Massv
	gotoxy(tabx +11, taby + 1); cout <<"MA SV";
	gotoxy(tabx + 20, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 20, taby + i); cout << char(179);
	}
	gotoxy(tabx+20, taby +2); cout << char(197);
	gotoxy(tabx+ 20, taby + tabh); cout << char(193);
	
	// Ho
	gotoxy(tabx +33, taby + 1); cout <<"HO SV";
	gotoxy(tabx + 50, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 50, taby + i); cout << char(179);
	}
	gotoxy(tabx+50, taby +2); cout << char(197);
	gotoxy(tabx+ 50, taby + tabh); cout << char(193);
	//Ten
	gotoxy(tabx +57, taby + 1); cout <<"TEN SV";
	gotoxy(tabx + 70, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 70, taby + i); cout << char(179);
	}
	gotoxy(tabx+70, taby +2); cout << char(197);
	gotoxy(tabx+ 70, taby + tabh); cout << char(193);
	
	// Diem tb
	gotoxy(tabx +74, taby + 1); cout <<"DIEM TB";
}

/*float TinChiCuaMonHoc(ListMonHoc lmh, string mamh)
{
	for(int i = 0; i < lmh.n; i++)
	{
		if(strcmp(lmh.DanhSachMon[i].MaMh, mamh.c_str()) == 0)
		{
			return atof(lmh.DanhSachMon[i].LT) + atof(lmh.DanhSachMon[i].TH);
		}
	}
}*/

/*float TinhDiemTrungBinh(ListDiemPtr &First, ListMonHoc lmh)
{
	float temp = 0, TongTinChi = 0;
	for(ListDiemPtr p = First; p != NULL; p = p->next)
	{
		TongTinChi += TinChiCuaMonHoc(lmh,p->data.MaMh);
		temp += atof(p->data.diem) * TinChiCuaMonHoc(lmh,p->data.MaMh);
	}
	return temp / TongTinChi;
}*/

float LamTron(float a) // den 2 so thap phan
{
    if (((int)(a*1000))%10 >= 5)
    {
        return ((float)((int)(a*100+1)))/100;
    }
    else
    {
        return ((float)((int)(a*100)))/100;
    }
}

/*void XuatDiemTB(ListLopTinChi llh, ListMonHoc lmh)
{
CHECKLOP:
	system("cls");
	 
	int vitrilop,vitrimon;
	LoadData_Lop(llh);

	LoadData_Diem(llh);
	
	Raw_Button_ESC(color_text1, color_fill1);
	Raw_Button_OK(color_text1, color_fill1);
	
	gotoxy(10,1);cout <<"    ____  _                   ______                          ____  _       __  ";
	gotoxy(10,2);cout <<"   / __ \\(_)__  ____ ___     /_  __/______  ______  ____ _   / __ )(_)___  / /_ ";
	gotoxy(10,3);cout <<"  / / / / / _ \\/ __ `__ \\     / / / ___/ / / / __ \\/ __ `/  / __  / / __ \\/ __ \\";
	gotoxy(10,4);cout <<" / /_/ / /  __/ / / / / /    / / / /  / /_/ / / / / /_/ /  / /_/ / / / / / / / /";
	gotoxy(10,5);cout <<"/_____/_/\\___/_/ /_/ /_/    /_/ /_/   \\__,_/_/ /_/\\__, /  /_____/_/_/ /_/_/ /_/ ";
	gotoxy(10,6);cout <<"                                                 /____/                         ";
	
	gotoxy(3,7); cout <<"   _____ _       __       _    ___               ________                  __              ";
	gotoxy(3,8); cout <<"  / ___/(_)___  / /_     | |  / (_)__  ____     /_  __/ /_  ___  ____     / /   ____  ____ ";
	gotoxy(3,9); cout <<"  \\__ \\/ / __ \\/ __ \\    | | / / / _ \\/ __ \\     / / / __ \\/ _ \\/ __ \\   / /   / __ \\/ __ \\";
	gotoxy(3,10); cout <<" ___/ / / / / / / / /    | |/ / /  __/ / / /    / / / / / /  __/ /_/ /  / /___/ /_/ / /_/ /";
	gotoxy(3,11);cout <<"/____/_/_/ /_/_/ /_/     |___/_/\\___/_/ /_/    /_/ /_/ /_/\\___/\\____/  /_____/\\____/ .___/ ";
	gotoxy(3,12);cout <<"                                                                                  /_/      ";
	
	
	if(vitrilop == -1){
		Raw_Button_ESC(color_text2, color_fill2);
		Sleep(500);
		return;
	}
	
	if(llh.LLTC[vitrilop].First == NULL)
	{
		gotoxy(boxx + 8 , 29); cout << " DANH SACH SINH VIEN LOP            HIEN CHUA CO! ";
		textcolor(30); gotoxy(boxx + 33 , 29); cout<<llh.LLTC[vitrilop].MaLop;
		Raw_Button_TAB(color_text1, color_fill1);
		int key;
		while(1)
		{
			key = getch();
			if(key == TAB || key == ENTER)
			{
				goto CHECKLOP;
			}
			else if(key == ESC)
			{
				Raw_Button_ESC(color_text2, color_fill2);
				Sleep(500);
				return;
			}
		}
	}
	
	gotoxy(36,13); textcolor(185); cout <<"      XAC NHAN MA LOP      ";
	Raw_Button_OK(color_text2, color_fill2);
	Sleep(500);
	Raw_Button_OK(color_text1, color_fill1);
	Raw_Button_TAB(color_text1, color_fill1);
	
	if(llh.LLTC[vitrilop].First->info.First == NULL)
	{
		textcolor(31);
		gotoxy(boxx + 8 , 29); cout << " DANH SACH DIEM CUA LOP            HIEN CHUA CO!";
		textcolor(30); gotoxy(boxx + 32 , 29); cout<<llh.LLTC[vitrilop].MaLop;
		Raw_Button_TAB(color_text1, color_fill1);
		int key;
		while(1){
			key = getch();
			if(key == TAB || key == ENTER)
			{
				goto CHECKLOP;
			}
			else if(key == ESC)
			{
				Raw_Button_ESC(color_text2, color_fill2);
				Sleep(500);
				return;
			}
		}
	}
	
	
	system("cls");
	 	
	
	int check = 0;
	
	textcolor(62);
	
	int TongSv =100;// TongSoSinhVien(llh.LLTC[vitrilop].First);
	int TongTrang, TrangHienTai = 0, i = 0;
	
	//SapXepSv(llh.LLTC[vitrilop].First);
	SinhVien sv[TongSv];
	for(ListSvPtr p = llh.LLTC[vitrilop].First; p != NULL; p = p->next)
	{
		LocDiem(p->info.First); // xoa diem rong
		sv[i] = p->info;
		i++;
	}
		
	TongTrang = (TongSv /21) + 1;
	
	int keyhit;
	
	while(1)
	{					
		system("cls");
		 
		Raw_Button_ESC(color_text1,color_fill1);
		Raw_Button_TAB(color_text1,color_fill1);
		TableDiemTB();
		
		gotoxy(20,2);
		gotoxy(25,2); textcolor(91); cout <<" BANG DIEM TRUNG BINH CUA LOP "; 
		gotoxy(tabx + 38, taby + tabh + 1); cout <<"Trang "<< TrangHienTai + 1<<"/"<<TongTrang;
		if(TrangHienTai > 0){
			gotoxy(tabx + 58, taby + tabh + 1); cout<<"["<<(char)ArrowUP<<"]"<<" Tro Lai |";
		}
		for(int i = TrangHienTai * 20, j = 3; i < 20 + TrangHienTai * 20 && i <TongSv; i++, j++)
		{
			gotoxy(tabx + 2, taby + j); cout << i+1;
			gotoxy(tabx +8, taby + j); cout << sv[i].MaSv;
			gotoxy(tabx + 22, taby + j); cout << sv[i].Ho;
			gotoxy(tabx + 52, taby + j); cout << sv[i].Ten;
			gotoxy(tabx + 75, taby + j);cout << LamTron(TinhDiemTrungBinh(sv[i].First, lmh));
		
		}
		keyhit = getch();
		
		if(keyhit == 224)
		{
			keyhit = getch();
			if(keyhit == UP){												
				if(TongTrang > 1 && TrangHienTai > 0)
				{
					TrangHienTai--;
					system("cls");
					continue;
				}
			}												
			else if(keyhit == DOWN){					
				if(TongTrang > 1 && TrangHienTai + 1 < TongTrang)
				{
					TrangHienTai++;
					system("cls");
					continue;
				}														
			}	
		}
		else if(keyhit == ESC)
		{
			Sleep(500);
			return;
		}
		else if(keyhit == TAB)
		{
			Sleep(500);
			goto CHECKLOP;
		}
	}
}*/



/*void TableDiemTongKet(ListMonHoc lmh, int tabd,int u)
{
	
	gotoxy(tabx, taby);	cout << char(218);
	for(int i = 1; i < tabd + 1; i++) cout << char(196);
	cout << char(191);
	for(int i = 1; i < tabh +1; i++) 
	{
		gotoxy(tabx, taby + i); cout << char(179);
	}
	
	for(int i = 1; i < tabh +1; i++) 
	{
		gotoxy(tabx + tabd + 1, taby + i); cout << char(179);
	}		
	gotoxy(tabx, taby + tabh); cout << char(192);
	for(int i = 1; i < tabd + 1; i++)
	{
		gotoxy(tabx + i, taby + tabh); cout << char(196);
	}
	gotoxy(tabx + tabd+1,taby +tabh); cout <<char(217);
	
	gotoxy(tabd - 6, taby + tabh + 1);	cout<<" Tiep Theo ["<<(char)ArrowDOWN<<"]";
	
	//--------------------------------------	
	//STT
	gotoxy(tabx +2, taby + 1); cout <<"STT";
	gotoxy(tabx + 6, taby); cout << char(194);	
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 6, taby + i); cout << char(179);
	}	
	gotoxy(tabx, taby +2); cout << char(195);	
	
	for(int i = 1; i < tabd + 1; i++) 
	{
		gotoxy(tabx +i, taby +2); cout << char(196);
	}
	gotoxy(tabx+ 6, taby +2); cout << char(197);
	gotoxy(tabx+ tabd +1, taby +2); cout << char(180);
	gotoxy(tabx+ 6, taby + tabh); cout << char(193);
	// Ho
	gotoxy(tabx +15, taby + 1); cout <<"HO SINH VIEN";
	gotoxy(tabx + 37, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 37, taby + i); cout << char(179);
	}
	gotoxy(tabx+37, taby +2); cout << char(197);
	gotoxy(tabx+ 37, taby + tabh); cout << char(193);
	
	// Ten
	gotoxy(tabx +39, taby + 1); cout <<"TEN SINH VIEN";
	gotoxy(tabx + 53, taby); cout << char(194);
	for(int i = 1; i < tabh + 1; i++) 
	{
		gotoxy(tabx + 53, taby + i); cout << char(179);
	}
	gotoxy(tabx+53, taby +2); cout << char(197);
	gotoxy(tabx+ 53, taby + tabh); cout << char(193);
	
	// diem
//	gotoxy(tabx +54, taby + 1); cout <<lmh.DanhSachMon[0].MaMh;
	for(int i = 0, j = 0; i < 4 + u; i++, j +=7)
	{
//		gotoxy(tabx +61 + j, taby + 1); cout <<lmh.DanhSachMon[i+1].MaMh;
		gotoxy(tabx + 60 + j, taby); cout << char(194);
		for(int i = 1; i < tabh + 1; i++)
		{
			gotoxy(tabx + 60 + j, taby + i); cout << char(179);
		}
		gotoxy(tabx+60 + j, taby +2); cout << char(197);
		gotoxy(tabx+ 60 + j, taby + tabh); cout << char(193);
	}
}*/

/*void XuatDiemTongKet(ListLopTinChi llh, ListMonHoc lmh)
{	
	int u,tabd = 87, t = 50;
	u = lmh.n - 5;
	if(u > 0)
	{
		t = u * t;
		tabd += 7 * u;
	}
	else
	{
		t = 0;
		u = 0;
	}
	 	
	resizeConsole(800 + t,600);
	LoadData_Lop(llh);
//	LoadData_Sv(llh);
	LoadData_Diem(llh);
	
CHECKLOP:
	system("cls");	
	 
	
	int vitrilop,vitrimon;

	gotoxy(2,1);cout <<"    ____  _                   ______                     __ __     __ ";
	gotoxy(2,2);cout <<"   / __ \\(_)__  ____ ___     /_  __/___  ____  ____ _   / //_/__  / /_";
	gotoxy(2,3);cout <<"  / / / / / _ \\/ __ `__ \\     / / / __ \\/ __ \\/ __ `/  / ,< / _ \\/ __/";
	gotoxy(2,4);cout <<" / /_/ / /  __/ / / / / /    / / / /_/ / / / / /_/ /  / /| /  __/ /_  ";
	gotoxy(2,5);cout <<"/_____/_/\\___/_/ /_/ /_/    /_/  \\____/_/ /_/\\__, /  /_/ |_\\___/\\__/  ";
	gotoxy(2,6);cout <<"                                            /____/                    ";	
	gotoxy(30,7); cout <<"  ________                  __  ___               __  __          ";
	gotoxy(30,8); cout <<" /_  __/ /_  ___  ____     /  |/  /___  ____     / / / /___  _____";
	gotoxy(30,9); cout <<"  / / / __ \\/ _ \\/ __ \\   / /|_/ / __ \\/ __ \\   / /_/ / __ \\/ ___/";
	gotoxy(30,10); cout<<" / / / / / /  __/ /_/ /  / /  / / /_/ / / / /  / __  / /_/ / /__  ";
	gotoxy(30,11);cout <<"/_/ /_/ /_/\\___/\\____/  /_/  /_/\\____/_/ /_/  /_/ /_/\\____/\\___/  ";
	
	
	if(vitrilop == -1){
		Sleep(500);
		return;
	}
	
	if(llh.LLTC[vitrilop].First == NULL)
	{
		gotoxy(boxx + 8 , 29); cout << " DANH SACH SINH VIEN LOP            HIEN CHUA CO! ";
		gotoxy(boxx + 33 , 29); cout<<llh.LLTC[vitrilop].MaLop;
		int key;
		while(1)
		{
			key = getch();
			if(key == TAB || key == ENTER)
			{
				goto CHECKLOP;
			}
			else if(key == ESC)
			{
				Sleep(500);
				return;
			}
		}
	}	
	gotoxy(36,13); cout <<"      XAC NHAN MA LOP      ";
	Raw_Button_OK(color_text2, color_fill2);
	Sleep(500);
	
	if(llh.LLTC[vitrilop].First->info.First == NULL)
	{
		gotoxy(boxx + 8 , 29); cout << " DANH SACH DIEM CUA LOP            HIEN CHUA CO!";
        gotoxy(boxx + 32 , 29); cout<<llh.LLTC[vitrilop].MaLop;
		int key;
		while(1){
			key = getch();
			if(key == TAB || key == ENTER)
			{
				goto CHECKLOP;
			}
			else if(key == ESC)
			{
				Sleep(500);
				return;
			}
		}
	}
	system("cls");
	int check = 0;	
	int TongSv =100;// TongSoSinhVien(llh.LLTC[vitrilop].First);
	int TongTrang, TrangHienTai = 0, i = 0;
	
	//SapXepSv(llh.LLTC[vitrilop].First);
	SinhVien sv[TongSv];
	for(ListSvPtr p = llh.LLTC[vitrilop].First; p != NULL; p = p->next)
	{
		LocDiem(p->info.First); 
		sv[i] = p->info;
		i++;
	}
		
	TongTrang = (TongSv /21) + 1;	
	int keyhit;
	
	while(1)
	{					
		system("cls");
		TableDiemTongKet(lmh,tabd,u);
		
		gotoxy(20,2);
		gotoxy(20,2); cout <<" BANG DIEM TONG KET THEO MON HOC CUA LOP ";
		gotoxy(61,2); 
		gotoxy(tabx + 38, taby + tabh + 1); cout <<"Trang "<< TrangHienTai + 1<<"/"<<TongTrang;
		if(TrangHienTai > 0)
		{
			gotoxy(tabd - 19, taby + tabh + 1); cout<<"["<<(char)ArrowUP<<"]"<<" Tro Lai |";
		}
		for(int i = TrangHienTai * 20, j = 3; i < 20 + TrangHienTai * 20 && i <TongSv; i++, j++)
		{
			gotoxy(tabx + 2, taby + j); cout << i+1;
			gotoxy(tabx + 8, taby + j); cout << sv[i].Ho;
			gotoxy(tabx + 39, taby + j); cout << sv[i].Ten;
			
			for(ListDiemPtr p = sv[i].First; p != NULL; p=p->next)
			{
				for(int u = 0,a = 0; u < lmh.n; u++,a+=7)
				{
					if(strcmp(p->data.MaMh,lmh.DanhSachMon[u].MaMh) == 0)
					{
						gotoxy(tabx + 55 + a, taby + j); cout << p->data.diem;
					}
				}
			}
		}
		keyhit = getch();		
		if(keyhit == 224)
		{
			keyhit = getch();
			if(keyhit == UP){												
				if(TongTrang > 1 && TrangHienTai > 0)
				{
					TrangHienTai--;
					system("cls");
					continue;
				}
			}												
			else if(keyhit == DOWN){					
				if(TongTrang > 1 && TrangHienTai + 1 < TongTrang)
				{
					TrangHienTai++;
					system("cls");
					continue;
				}														
			}	
		}
		else if(keyhit == ESC)
		{
			Sleep(500);
			return;
		}
		else if(keyhit == TAB)
		{
			Sleep(500);
			goto CHECKLOP;
		}
	}	
}*/

void Menu_main()
{
	int x = 22, y = 17;
	int key;
	char menu_text[11][100] = { "1 MO LOP TIN CHI ","3 NHAP SINH VIEN "," NHAP MON HOC ", " NHAP DIEM "," IN DANH SACH LOP THEO NIEN KHOA ","4 IN DANH SACH SINH VIEN THEO LOP ",
	" IN DANH SACH MON HOC "," BANG DIEM THEO LAN THI "," BANG DIEM TRUNG BINH CUOI KHOA CUA LOP THEO TIN CHI "," BANG DIEM TONG KET CAC MON CUA SINH VIEN THEO LOP "," KET THUC CHUONG TRINH " };	
MENU:
	system("cls");
	resizeConsole(1000,600);
	SetConsoleTitle("Quan Ly Sinh Vien Theo He Tin Chi");
	
	ListLopTinChi llh;
	ListSv l;
	ListMonHoc lmh;
	ListSvPtr First;
	LoadData_Lop(llh);
	LoadData_Sv(l);
	LoadData_Mh(lmh);
	gotoxy(1,1); cout<<"    ______                        __    _    _____ _       __     _    ___  ";
	gotoxy(1,2); cout<<"   / ____ \\__  __ ___  _____     / /   (_)  / ___/(_)___  / /_   | |  / (_)__  ____ " ;
	gotoxy(1,3); cout<<"  / /   / / / / / __ `/ __  /   / /   / /   \\__ \\/ / __ \\/ __ \\  | | / / / _ \\/ __ \\";
	gotoxy(1,4); cout<<" / /___/ / /_/ / /_/ / / / /   / /___/ /   ___/ / / / / / / / /  | |/ / /  __/ / / / ";
	gotoxy(1,5); cout<<" \\_____\\_\\__,_/\\__,_/_/ /_/   /_____/_/   /____/_/_/ /_/_/ /_/   |___/_/\\___/_/ /_/  ";
	gotoxy(1,6); cout <<"  ________                   __                 ____ _          _____ __    _";
	gotoxy(1,7); cout <<" /_  __/ /_  ___  ____      / /   ____  ____   /__  (_)___     / ___ / /_  (_)";
	gotoxy(1,8);  cout<<"  / / / __ \\/ _ \\/ __ \\    / /   / __ \\/ __ \\    / / / __ \\   / /   / __ \\/ /";
	gotoxy(1,9); cout <<" / / / / / /  __/ /_/ /   / /___/ /_/ / /_/ /   / / / / / /  / /___/ / / / /";
	gotoxy(1,10);cout <<"/_/ /_/ /_/\\___/\\____/   /_____/\\____/ .___/   /_/_/_/ /_/   \\____/_/ /_/_/";
	gotoxy(1,11);cout <<"                                    /_/                           ";
	
	gotoxy(x+19, 17); cout <<menu_text[0];
	gotoxy(x+12, 18); cout <<menu_text[1];
	gotoxy(x+17, 19); cout << menu_text[2];
	gotoxy(x+18, 20); cout << menu_text[3];
	gotoxy(x+8, 21); cout << menu_text[4];
	gotoxy(x+8, 22); cout << menu_text[5];
	gotoxy(x+14, 23); cout << menu_text[6];
	gotoxy(x+13, 24); cout <<  menu_text[7];
	gotoxy(x, 25); cout << menu_text[8];
	gotoxy(x, 26); cout << menu_text[9];
	gotoxy(x+14, 27); cout << menu_text[10];

	do {
		gotoxy(x, y);
		switch (y)
		{
			case 17: gotoxy(x+19, 17); cout << menu_text[0]; break;
			case 18: gotoxy(x+12, 18); cout << menu_text[1]; break;
			case 19: gotoxy(x+17, 19); cout <<menu_text[2]; break;
			case 20: gotoxy(x+18, 20); cout << menu_text[3]; break;
			case 21: gotoxy(x+8, 21); cout << menu_text[4]; break;
			case 22: gotoxy(x+8, 22); cout << menu_text[5]; break;
			case 23: gotoxy(x+14, 23); cout << menu_text[6]; break;
			case 24: gotoxy(x+13, 24); cout << menu_text[7]; break;
			case 25: gotoxy(x, 25); cout << menu_text[8]; break;
			case 26: gotoxy(x, 26); cout << menu_text[9]; break;
			case 27: gotoxy(x+14, 27); cout << menu_text[10];break;
		}
		key = getch();
		if (key == UP)
		{
			gotoxy(x, y);
			switch (y)
			{ 
				case 17: gotoxy(x+19, 17); cout << menu_text[0]; break;
				case 18: gotoxy(x+12, 18); cout << menu_text[1]; break;
				case 19: gotoxy(x+17, 19); cout <<menu_text[2]; break;
				case 20: gotoxy(x+18, 20); cout << menu_text[3]; break;
				case 21: gotoxy(x+8, 21); cout << menu_text[4]; break;
				case 22: gotoxy(x+8, 22); cout << menu_text[5]; break;
				case 23: gotoxy(x+14, 23); cout << menu_text[6]; break;
				case 24: gotoxy(x+13, 24); cout << menu_text[7]; break;
				case 25: gotoxy(x, 25); cout << menu_text[8]; break;
				case 26: gotoxy(x, 26); cout << menu_text[9]; break;
				case 27: gotoxy(x+14, 27); cout << menu_text[10];break;
			}
			y--;
			if (y < 17)
			{
				y = 27;
			}
		}
		else if (key == DOWN)
		{
			gotoxy(x, y);
			switch (y)
			{
				case 17: gotoxy(x+19, 17); cout << menu_text[0]; break;
				case 18: gotoxy(x+12, 18); cout << menu_text[1]; break;
				case 19: gotoxy(x+17, 19); cout <<menu_text[2]; break;
				case 20: gotoxy(x+18, 20); cout << menu_text[3]; break;
				case 21: gotoxy(x+8, 21); cout << menu_text[4]; break;
				case 22: gotoxy(x+8, 22); cout << menu_text[5]; break;
				case 23: gotoxy(x+14, 23); cout << menu_text[6]; break;
				case 24: gotoxy(x+13, 24); cout << menu_text[7]; break;
				case 25: gotoxy(x, 25); cout << menu_text[8]; break;
				case 26: gotoxy(x, 26); cout << menu_text[9]; break;
				case 27: gotoxy(x+14, 27); cout << menu_text[10];break;
			}
			y++;
			if (y > 27)
			{
				y = 17;
			}
		}
		if(y == 17 && key == ENTER) // nhap lop tin chi
		{
			NhapLop(llh);
			goto MENU;
		}
		else if(y == 18 && key == ENTER) // nhap sv
		{
			NhapSv(l);
			goto MENU;
		}
		else if(y == 19 && key == ENTER) // nhap mon
		{
			NhapMon(lmh);
			goto MENU;
		}
		else if(y == 20 && key == ENTER) // nhap diem
		{
			//NhapDiem(llh,lmh);
			goto MENU;
		}
		else if(y == 21 && key == ENTER) // in lop
		{
			XuatLopTheoNienKhoa(llh); 
			goto MENU;
		}
		else if(y == 22 && key == ENTER) // in sv
		{
			XuatThongTinSv(l,llh);
			goto MENU;
		}
		else if(y == 23 && key == ENTER) // in mon hoc
		{
		//	XuatMonHoc(llh, lmh);
			goto MENU;
		}
		else if(y ==24 && key == ENTER) // in diem theo lan
		{	
		//	XuatDiemTheoLanThi(llh,lmh);
			goto MENU;	
		}
		else if(y == 25 && key == ENTER) // in diem trung binh theo tin chi
		{
		//	XuatDiemTB(llh,lmh);
			goto MENU;
		}
		else if(y == 26 && key == ENTER) // in diem tong ket mon
		{
		//	XuatDiemTongKet(llh,lmh);
			goto MENU;
		}	
		else if(y == 27 && key == ENTER) // ket thuc chuong trinh
		{
			exit(0);
		}		
	} while (true);
}

int main()
{
	Menu_main();
	system("pause");
	return 0;
}
