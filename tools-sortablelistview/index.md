---
title: "Tools.SortableListView"
date: "2017-09-03"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

Imports System.Collections
Imports System.Windows.Forms
Namespace Tools
  Public Class SortableListView
    Inherits System.Windows.Forms.ListView
#Region "Constructors"
    Public Sub New()
      MyBase.New()
      LvwColumnSorter = New ListViewColumnSorter(Me)
    End Sub
#End Region
#Region "Data definition"
    Public Property FixedStringForLastItem() As String
      '
      ' Fixed string for the last item, so it will stay the last item
      '
      Get
        Return LvwColumnSorter.FixedStringForLastItem
      End Get
      Set(ByVal Value As String)
        '
        ' The IComparer has to know that
        '
        LvwColumnSorter.FixedStringForLastItem = Value
      End Set
    End Property
    Public Property SortColumn() As Integer
      '
      ' Index of sort column
      '
      Get
        Return LvwColumnSorter.SortColumn
      End Get
      Set(ByVal Value As Integer)
        '
        ' Put it through to IComparer
        '
        LvwColumnSorter.SortColumn = Value
        '
        ' Let's do it
        '
        If LvwColumnSorter.Order = SortOrder.None Then
          MyBase.ListViewItemSorter = Nothing
        Else
          If MyBase.ListViewItemSorter Is Nothing Then
            MyBase.ListViewItemSorter = LvwColumnSorter
          Else
            MyBase.Sort()
          End If
        End If
        '
      End Set
    End Property
    Public Property Order() As SortOrder
      '
      ' Sort order (none, ascending or descending)
      '
      Get
        Return LvwColumnSorter.Order
      End Get
      Set(ByVal Value As SortOrder)
        '
        ' Last information needed for IComparer
        '
        LvwColumnSorter.Order = Value
        If Value = SortOrder.None Then
          If LvwColumnSorter.SortColumn > -1 Then
            '
            ' Setting the text causes a repaint of the header
            '
            MyBase.Columns(LvwColumnSorter.SortColumn).Text = \_
                MyBase.Columns(LvwColumnSorter.SortColumn).Text
            '
            ' If there has been a triangle from a previous sorting,
            ' it is now deleted
            '
          End If
          LvwColumnSorter.SortColumn = -1
        End If
        '
        ' Let's do it
        '
        If LvwColumnSorter.SortColumn = -1 Then
          MyBase.ListViewItemSorter = Nothing
        Else
          If MyBase.ListViewItemSorter Is Nothing Then
            MyBase.ListViewItemSorter = LvwColumnSorter
          Else
            MyBase.Sort()
          End If
        End If
        '
      End Set
    End Property
    Private WithEvents LvlListener As LvlListenerClass
    Private WithEvents HdrListener As HdrListenerClass
    Private LvwColumnSorter As ListViewColumnSorter
#End Region
#Region "Event handling"
    Public Event Scroll(ByVal sender As Object, ByVal e As EventArgs)
    Public Shadows Event Paint(ByVal sender As Object, ByVal e As EventArgs)
    Private Class LvlListenerClass
      '
      ' Listen for operating system messages to raise the events
      ' "Scroll" or "Paint" for the ListView
      '
      Inherits NativeWindow
      Public Event Scroll(ByVal sender As Object, ByVal e As EventArgs)
      Public Event Paint(ByVal sender As Object, ByVal e As EventArgs)
      Const WM\_HSCROLL = &H114
      Const WM\_VSCROLL = &H115
      Const WM\_PAINT = &HF
      Private Ctrl As Control
      Public Sub New(ByVal Ctrl As Control)
        AssignHandle(Ctrl.Handle)
      End Sub
      Protected Overrides Sub WndProc(ByRef m As Message)
        MyBase.WndProc(m)
        If m.Msg = WM\_HSCROLL Or m.Msg = WM\_VSCROLL Then
          RaiseEvent Scroll(Ctrl, New EventArgs())
        End If
        If m.Msg = WM\_PAINT Then
          RaiseEvent Paint(Ctrl, New EventArgs())
        End If
      End Sub
      Protected Overrides Sub Finalize()
        ReleaseHandle()
        MyBase.Finalize()
      End Sub
    End Class
    Private Class HdrListenerClass
      '
      ' Listen for operating system messages to raise the event
      ' "HaederPaint", when the column headers are to be painted.
      ' On this event, a triangle, symbolizing sort order and
      ' column is to be drawn.
      '
      Inherits NativeWindow
      Public Event HaederPaint(ByVal sender As Object, ByVal e As EventArgs)
      Const WM\_PAINT = &HF
      Private CtrlValue As Control
      Public Sub New(ByVal Ctrl As Control, ByVal HeaderHandle As System.IntPtr)
        AssignHandle(HeaderHandle)
        CtrlValue = Ctrl
      End Sub
      Protected Overrides Sub WndProc(ByRef m As Message)
        MyBase.WndProc(m)
        If m.Msg = WM\_PAINT Then
          RaiseEvent HaederPaint(CtrlValue, New EventArgs())
        End If
      End Sub
      Protected Overrides Sub Finalize()
        ReleaseHandle()
        MyBase.Finalize()
      End Sub
    End Class
    Protected Overrides Sub OnHandleCreated(ByVal e As EventArgs)
      '
      ' Now it's the right time to do some initializations
      '
      MyBase.OnHandleCreated(e)
      If Not Me.DesignMode Then
        LvlListener = New LvlListenerClass(Me)
        LvwColumnSorter.GetHeaderHandle()
        HdrListener = New HdrListenerClass(Me, LvwColumnSorter.HeaderHandle)
      End If
    End Sub
    Private Sub LvlListener\_Paint(ByVal sender As Object, ByVal e As System.EventArgs) \_
            Handles LvlListener.Paint
      '
      ' Make this event public
      '
      RaiseEvent Paint(sender, e)
      '
    End Sub
    Private Sub LvlListener\_Scroll(ByVal sender As Object, ByVal e As System.EventArgs) \_
            Handles LvlListener.Scroll
      '
      ' Make this event public
      '
      RaiseEvent Scroll(sender, e)
      '
    End Sub
    Private Sub HdrListener\_HaederPaint(ByVal sender As Object, \_
            ByVal e As System.EventArgs) Handles HdrListener.HaederPaint
      '
      ' The column headers has been painted, so draw the sort triangle
      '
      LvwColumnSorter.VisualizeOrder()
      '
    End Sub
    Private Sub MyBase\_ColumnClick(ByVal sender As Object, ByVal e As  \_
            System.Windows.Forms.ColumnClickEventArgs) Handles MyBase.ColumnClick
      '
      ' The user wants to sort the list view items
      '
      If (e.Column = SortColumn) Then
        '
        ' The column has been clicked twice, so switch the sort order
        '
        If (Order = SortOrder.Ascending) Then
          Order = SortOrder.Descending
        Else
          Order = SortOrder.Ascending
        End If
      Else
        '
        ' It has to be sort ascending by the column
        '
        SortColumn = e.Column
        Order = SortOrder.Ascending
      End If
      '
    End Sub
#End Region
#Region "API stuff"
    '
    ' The API function ChildWindowFromPoint is used to find out the window handle of
    ' the columns header.
    '
    Private Structure GdiPoint
      Dim x As Integer
      Dim y As Integer
    End Structure
    Private Declare Function ChildWindowFromPoint Lib "user32" \_
        (ByVal hWndParent As System.IntPtr, ByVal Point As GdiPoint) As System.IntPtr
    '
#End Region
#Region "Subroutines and functions"
    Private Class ListViewColumnSorter
      Implements System.Collections.IComparer
      '
      ' This class implements an non case sensitve IComparer for
      ' sorting items in a ListView
      '
      Private ColumnToSort As Integer
      Private OrderOfSort As SortOrder
      Private ObjectCompare As CaseInsensitiveComparer
      Private FixedStringForLastItemValue As String
      Private MyListViewValue As SortableListView
      Public Sub New(ByVal MyListView As SortableListView)
        ColumnToSort = 0
        OrderOfSort = SortOrder.None
        ObjectCompare = New CaseInsensitiveComparer()
        MyListViewValue = MyListView
      End Sub
      Public Property FixedStringForLastItem() As String
        '
        ' Fixed string for the last item, so it will stay the last item
        '
        Get
          Return FixedStringForLastItemValue
        End Get
        Set(ByVal Value As String)
          FixedStringForLastItemValue = Value
        End Set
      End Property
      Public Property SortColumn() As Integer
        '
        ' Index of sort column
        '
        Set(ByVal Value As Integer)
          ColumnToSort = Value
        End Set
        Get
          Return ColumnToSort
        End Get
      End Property
      Public Property Order() As SortOrder
        '
        ' Sort order (none, ascending or descending)
        '
        Set(ByVal Value As SortOrder)
          OrderOfSort = Value
          VisualizeOrder()
        End Set
        Get
          Return OrderOfSort
        End Get
      End Property
      Public ReadOnly Property HeaderHandle() As System.IntPtr
        '
        ' The handle of the columns header
        '
        Get
          Return HeaderHandleValue
        End Get
      End Property
      Public Sub GetHeaderHandle()
        '
        ' Find out the handle of the columns header
        '
        Dim TestPoint As GdiPoint
        TestPoint.x = 5
        TestPoint.y = 5
        HeaderHandleValue = \_
            ChildWindowFromPoint(MyListViewValue.Handle, TestPoint)
      End Sub
      Public Function Compare(ByVal a As Object, ByVal b As Object) As \_
              Integer Implements IComparer.Compare
        '
        ' Using a case insensitive comparer to compare the Text of
        ' two list view items
        '
        If OrderOfSort = SortOrder.None Then
          '
          ' Return 0 means that both items are equal, so no
          ' change of the sequence will occure
          '
          Return 0
          '
        End If
        '
        Dim Result As Integer
        Dim LvIa As ListViewItem = a
        Dim LvIb As ListViewItem = b
        Dim Ca As String = LvIa.SubItems(ColumnToSort).Text
        Dim Cb As String = LvIb.SubItems(ColumnToSort).Text
        '
        ' Compare the two strings
        '
        Result = ObjectCompare.Compare(Ca, Cb)
        If FixedStringForLastItemValue Is Nothing Then
          ' nothing
        Else
          If Cb = FixedStringForLastItemValue Then
            Return -1
          End If
        End If
        '
        ' If the strings contains numbers, correct the sequence. For example:
        ' 3, 20, 1 has to be sorted as 1, 3, 20 and not 1, 20, 3
        '
        If Result <> 0 And IsNumeric(Ca) And IsNumeric(Cb) Then
          If (CDbl(Ca) > CDbl(Cb)) Then
            Result = 1
          Else
            Result = -1
          End If
        End If
        '
        ' The return value depends on the sort order
        '
        Select Case OrderOfSort
          Case SortOrder.Ascending
            '
            ' As ascending sort is desired, return the compare result
            '
            Return Result
            '
          Case SortOrder.Descending
            '
            ' As descending sort is desired, compare result is to be
            ' turned in the negative
            '
            Return -Result
            '
        End Select
        Return Result
      End Function
      Public Sub VisualizeOrder()
        '
        ' The sort column and order are visualized by a little triangle,
        ' which is to be drawn right to the columns name
        '
        If ColumnToSort = -1 Then
          Exit Sub
        End If
        '
        ' Draw the triangle in the header. Before that,
        ' make theold triangle invisible. After that, store the
        ' position of the triangle for usage at the next time.
        '
        If SortColumnOld > -1 And SortColumnOld <> ColumnToSort Then
          '
          ' Setting the text causes a repaint of the header
          '
          MyListViewValue.Columns(SortColumnOld).Text = \_
              MyListViewValue.Columns(SortColumnOld).Text.TrimEnd
          '
        End If
        If OrderOfSort = SortOrder.None Then
          SortColumnOld = -1
        Else
          Dim Grx As Graphics
          Grx = Graphics.FromHwnd(HeaderHandle)
          Dim LblMeasureHeader As New Label()
          LblMeasureHeader.Font = MyListViewValue.Font
          LblMeasureHeader.AutoSize = True
          If MyListViewValue.Columns(ColumnToSort).TextAlign <> \_
              HorizontalAlignment.Left Then
            If MyListViewValue.Columns(ColumnToSort).Text.TrimEnd = \_
                    MyListViewValue.Columns(ColumnToSort).Text Then
              MyListViewValue.Columns(ColumnToSort).Text += Space(5)
            End If
          End If
          LblMeasureHeader.Text = MyListViewValue.Columns(ColumnToSort).Text
          Dim x, y, i As Integer
          Dim Triangle(2) As Point
          '
          ' Evaluate the length of the Name
          '
          x = LblMeasureHeader.Width + 13
          '
          ' Keep a minimum distance of the right bounce
          '
          If x + 15 > MyListViewValue.Columns(ColumnToSort).Width Then
            x = MyListViewValue.Columns(ColumnToSort).Width - 13
          End If
          '
          ' Sum the width of all columns left to the sort column
          '
          For i = 0 To ColumnToSort - 1
            x += MyListViewValue.Columns(i).Width
          Next
          '
          ' Top is determined by the text height
          '
          y = LblMeasureHeader.Height / 2 + 2
          '
          ' The three points of the triangle are depending on
          ' the sort order
          '
          If OrderOfSort = SortOrder.Ascending Then
            Triangle(0).X = x - 1
            Triangle(0).Y = y + 4
            Triangle(1).X = x + 9
            Triangle(1).Y = y + 4
            Triangle(2).X = x + 4
            Triangle(2).Y = y - 2
          Else
            Triangle(0).X = x
            Triangle(0).Y = y - 1
            Triangle(1).X = x + 9
            Triangle(1).Y = y - 1
            Triangle(2).X = x + 4
            Triangle(2).Y = y + 4
          End If
          Grx.FillPolygon(SystemBrushes.ControlDark, Triangle)
          SortColumnOld = ColumnToSort
          Grx.Dispose()
        End If
      End Sub
      Private SortColumnOld As Integer = -1
      Private HeaderHandleValue As System.IntPtr
    End Class
#End Region
  End Class
End Namespace
