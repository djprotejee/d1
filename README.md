Decomposition = class (TForm)
  private

  function f(x: Real): Real;
  Procedure TabF(var Xe: Vector; var Ye: Vector);
  Procedure Furje(Xe, Ye: Vector; Ne: Integer; var Yg: Vector);
  function Simpson(al, bl: Real; Ne: Integer): Real;
  public
end;
  TForm1 = class(TForm)
    Image1: TImage;
    ComboBox1: TComboBox;
    ComboBox2: TComboBox;
    GroupBox1: TGroupBox;
    Label1: TLabel;
    Label2: TLabel;
    Label3: TLabel;
    Label4: TLabel;
    Label5: TLabel;
    Label7: TLabel;
    Label8: TLabel;
    Label9: TLabel;
    Label10: TLabel;
    Label13: TLabel;
    Label11: TLabel;
    Label14: TLabel;
    Edit1: TEdit;
    Edit2: TEdit;
    Edit3: TEdit;
    Edit4: TEdit;
    BitBtn1: TBitBtn;
    SpinEdit1: TSpinEdit;
    SpinEdit2: TSpinEdit;
    SpinEdit3: TSpinEdit;
    SpinEdit4: TSpinEdit;
    Button1: TButton;
    Button2: TButton;
    procedure Button1Click(Sender: TObject);
    procedure Button2Click(Sender: TObject);
  private
    { Private declarations }
  public
    { Public declarations }
  end;
var
  Form1: TForm1;
  F_GRF: Zapys; // змінна типу Record
implementation
{$R *.dfm}
{ TDecomposition }
Function Decomposition.f(x: Real): Real;
  Begin
  With F_GRF do // оператор приєднання: для спрощення доступу до полiв запису
 Begin
  Tp := bl - al;
  case k1 of
  0:
      begin
        if x < TP / 2 then f:= 2
        else
        if(x >= TP / 2) and (x < 3 * TP / 4) then
        f := 4 * (TP - 2 * x) / TP
        else
        f := 8 * (x - TP) / TP;
      end;
    1:
      begin
       if (x < Tp / 2) then
        begin
          if (x * 3 < Tp / 4) then
          f:= 4 * (Tp - 2 * x) / Tp
        else f:=2;
        end
      else
          f:= 8 * (x - Tp) / Tp;
      end;
    2:
      begin
      f:=3 * x / (2 * Tp);
      end;
    3:
      begin
        if ((x >= Tp / 4) and (x * 3 < Tp / 4)) then
        begin
          f:=2 * (2 * Tp - 3 * x) / Tp;
        end
        else
        begin
            f:=8 * (x - Tp) / Tp;
        end;
      end;
    end;
 End;

  end;
 function Decomposition.Simpson(al, bl: Real; Ne: Integer): Real;
var
  i: Integer;
  h, x, sum: Real;
begin
  h := (bl - al) / Ne;
  sum := f(al) + f(bl);
  x := al;
  for i := 1 to Ne - 1 do
  begin
    x := x + h;
    if i mod 2 = 0 then
      sum := sum + 2 * f(x)
    else
      sum := sum + 4 * f(x);
  end;
  Result := sum * h / 3;
end;
Procedure Decomposition.TabF(var Xe: Vector; var Ye: Vector);// процедура табулювання періодичної функції
Var h: Real;
i: Integer;
  Begin
  With F_GRF do // оператор приєднання: для спрощення доступу до полiв запису
    Begin
       case k2 of
      0:
        begin
           h := (bl - al) / Ne;
          Xe[0]:= al;
          For i := 0 to Ne - 1 do
            Begin
            Ye[i] := f(Xe[i]);
            Xe[i+1] := Xe[i] + h;
            End;
        end;
        1:
        begin
           h := (bl - al) / Ne;
            Xe[0] := al;
            for i := 0 to Ne - 1 do
            begin
              Ye[i] := (f(Xe[i]) + f(Xe[i+1])) * h / 2; // обчислення методом трапецій
              Xe[i+1] := Xe[i] + h;
            end;
        end;
      2:
      begin
        h := (bl - al) / Ne;
        Xe[0] := al;
        for i := 0 to Ne - 1 do
        begin
          Ye[i] := Simpson(al, bl, Ne); // виклик функції Simpson
          Xe[i+1] := Xe[i] + h;
        end;
      end;
       end;

     End;
  End;
// Процедура побудови і табулювання ряду Фур’є згідно з алгоритмом, що є на рис. 4.
Procedure Decomposition.Furje(Xe, Ye: Vector; Ne: Integer; var Yg: Vector);
Var i,num, k: Integer;
w, KOM, S, G, D: Real;
  Begin
  With F_GRF do // оператор приєднання: для спрощення доступу до полiв запису
   Begin
   // вводимо кількість гармонік
    if TryStrToInt(Form1.Edit2.text, num) then
    begin
      Ng:=num;
    end
    else
    begin
       ShowMessage('Рядок Ng не є числом');
       Halt;
    end;
    TP := bl - al; // TP – період нашої функції
    // Обчислення коефіцієнтів ряду Фур'є
    w := 2 * Pi / TP;
    For k := 1 to Ng do
      Begin
      KOM := k * w;
      G := 0.0; D := 0.0;
      For i :=1 to Ne do
        Begin
        S := KOM * Xe[i];
        G := G + Ye[i] * Cos(S);
        D := D + Ye[i] * Sin(S);
        End;
      a[k] := 2 * G / Ne;
      b[k] := 2 * D / Ne;
      c[k] := Sqrt(Sqr(a[k]) + Sqr(b[k]));
      End;
    a[0] := 0.0;
    For i := 1 to Ne do
    a[0] := a[0] + Ye[i];
    a[0] := a[0] / Ne;
    For i := 0 to Ne - 1 do // побудова і табулювання суми ряду Фур’є
      Begin
      S := 0; D := Xe[i] * w;
      For k:=1 to Ng do
        Begin
        KOM := k * D;
        S := S + b[k] * Sin(KOM) + a[k] * Cos(KOM);
        End;
      Yg[i] := a[0] + S;
      End;
   End;
End;
procedure TForm1.Button1Click(Sender: TObject);
Var Ft : File of Zapys; // опис типізованого файлу
 i,integ : Integer;
 flot:Double;
 decomp: Decomposition;
Begin // початок виконуваної частини процедури TForm1.Button1Click
 With F_GRF do // оператор приєднання With для спрощення доступу до полiв запису F_GRF
 Begin
  if TryStrToInt(Edit1.Text, integ) then
    begin
      Ne := integ;
    end
  else
    begin
       ShowMessage('Рядок Ne не є числом');
       exit;
    end;
  if TryStrToFloat(Edit3.Text, flot) then
    begin
      Al:=flot;
    end
  else
  begin
     ShowMessage('Рядок al не є числом');
     exit;
  end;
  if TryStrToFloat(Edit4.Text, flot) then
    begin
      Bl:=flot;
    end
  else
  begin
     ShowMessage('Рядок bl не є числом');
     exit;
  end;
 Ngr:= Ne;
 case ComboBox1.ItemIndex of
      0:k1:=0;
      1:k1:=1;
      2:k1:=2;
      3:k1:=3;
      end;
 decomp.TabF( Xe, Ye); // табуляція заданої періодичної функції
 decomp.Furje(Xe,Ye,Ne,Yg); // обчислення коефіцієнтів ряду Фур'є і табуляція суми ряду
// Будуємо графіки обох функцій Ye(Xe) та Yg(Xe)
 L := 40; // відступ від країв компоненти TImage
 With Form1.Image1 do
 Begin
 Canvas.Pen.Width := 2;
 Canvas.Pen.Color := clBlue;
 Canvas.Pen.Style := psSolid;
 Canvas.Rectangle(1, 1, Width - 1, Height - 1);
 MinYg:=Yg[0]; MaxYg:=Yg[0];
 For i := 1 to Ngr - 1 do
 Begin
 If MaxYg < Yg[i] then MaxYg := Yg[i];
 If MinYg > Yg[i] then MinYg := Yg[i];
 End;
 MinX := Xe[0]; MaxX := Xe[Ne-1];
 MinY := Ye[0]; MaxY := Ye[0];
 For i := 1 to Ne - 1 do
 Begin
 If MaxY< Ye[i] then MaxY := Ye[i];
If MinY> Ye[i] then MinY := Ye[i];
 End;
 If MaxY < MaxYg then MaxY := MaxYg;
 If MinY > MinYg then MinY := MinYg;
// Обчислюємо значення коефіцієнтів масштабування
 Kx := (Width - 2 * L) /(MaxX - MinX);
 Ky := (Height - 2 * L) /(MinY - MaxY);
 Zx := (Width * MinX - L * (MinX + MaxX)) / (MinX - MaxX);
 Zy := (Height * MaxY - L * (MinY + MaxY)) / (MaxY - MinY);
 End;
 // Робота з типізованим файлом даних для побудови графіків--------------
 AssignFile (Ft , 'Grf_file.dat'); // iнiцiалізація файлу
 Rewrite ( Ft ); // створення типізованого файлу
 Write (Ft, F_GRF); // запис у типізованого файл
 CloseFile (Ft ); // закриття файлу
 ShowMessage('Дані успішно записано у файл');

 End;
end;
