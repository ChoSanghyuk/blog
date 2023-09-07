# Excel 시트이름 일괄적으로 수정하기



엑셀 시트의 이름을 일괄적으로 변경할 필요가 있어, 엑셀 VBA 코드에 대해서 찾아보았다.





```vb
Sub 매크로()
    Dim i As Integer
        For i = 1 To Sheets.Count
        With Sheets(i)
            if i < 10 Then
                .Name = "시트번호_00" & i
            Else
                .Name = "시트번호_0" & i
            END IF
        END With
	Next i
End Sub
```

