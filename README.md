# NOTES 
CUSTOME CALENDAR...!!!
HomePage.xmal
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Maui.Controls;assembly=SkiaSharp.Views.Maui.Controls"
             x:Class="MauiApp1.HomePage"
             BackgroundColor="White">
    <StackLayout Padding="10" Spacing="10">
        <StackLayout Orientation="Horizontal" HorizontalOptions="Center" Spacing="10">
            <Image Source="calander_icon.png"
                   HeightRequest="48"
                   HorizontalOptions="Center"
                   VerticalOptions="Center">
                <Image.GestureRecognizers>
                    <TapGestureRecognizer Tapped="CalendarIcon_Tap" />
                </Image.GestureRecognizers>
            </Image>
            <Label x:Name="SelectedDateLabel"
                   FontSize="18"
                   VerticalOptions="Center"
                   TextColor="Black"
                   Text="Select a date" />
        </StackLayout>

        <Frame x:Name="CalendarPopup"
               IsVisible="False"
               BackgroundColor="White"
               CornerRadius="20"
               Padding="0"
               HorizontalOptions="Center"
               VerticalOptions="Center"
               WidthRequest="300"
               HeightRequest="380"
               HasShadow="True">
            <StackLayout>
                <StackLayout Orientation="Horizontal"
                             HorizontalOptions="Center"
                             BackgroundColor="Purple"
                             Padding="5"
                             Spacing="20">
                    <Label Text="&#xf104;"
                           FontFamily="FontAwesome"
                           FontSize="32"
                           TextColor="White"
                           HorizontalOptions="Center">
                        <Label.GestureRecognizers>
                            <TapGestureRecognizer Tapped="PrevButton_Tap" />
                        </Label.GestureRecognizers>
                    </Label>

                    <Label x:Name="MonthLabel"
                           FontSize="24"
                           TextColor="White"
                           HorizontalOptions="CenterAndExpand"
                           VerticalOptions="Center">
                        <Label.GestureRecognizers>
                            <TapGestureRecognizer Tapped="MonthLabel_Tap" />
                        </Label.GestureRecognizers>
                    </Label>

                    <Label x:Name="YearLabel"
                           FontSize="24"
                           TextColor="White"
                           HorizontalOptions="CenterAndExpand"
                           VerticalOptions="Center">
                        <Label.GestureRecognizers>
                            <TapGestureRecognizer Tapped="YearLabel_Tap" />
                        </Label.GestureRecognizers>
                    </Label>

                    <Label Text="&#xf105;"
                           FontFamily="FontAwesome"
                           FontSize="32"
                           TextColor="White"
                           HorizontalOptions="Center">
                        <Label.GestureRecognizers>
                            <TapGestureRecognizer Tapped="NextButton_Tap" />
                        </Label.GestureRecognizers>
                    </Label>
                </StackLayout>

                <ScrollView x:Name="MonthSelectionPopup" IsVisible="False" HeightRequest="260">
                    <StackLayout />
                </ScrollView>

                <ScrollView x:Name="YearSelectionPopup" IsVisible="False" HeightRequest="260">
                    <StackLayout />
                </ScrollView>

                <skia:SKCanvasView x:Name="CalendarCanvas"
                                   HeightRequest="260"
                                   PaintSurface="CalendarCanvas_PaintSurface"
                                   EnableTouchEvents="True"
                                   Touch="CalendarCanvas_Touch" />

                <StackLayout Orientation="Horizontal" HorizontalOptions="End" Padding="10" Spacing="10">
                    <Button Text="Close" Clicked="CloseButton_Click" BackgroundColor="LightGray" TextColor="Black" WidthRequest="100" />
                    <Button Text="OK" Clicked="OkButton_Click" BackgroundColor="Purple" TextColor="White" WidthRequest="100" />
                </StackLayout>
            </StackLayout>
        </Frame>
    </StackLayout>
</ContentPage>
HomePage.xmal.cs
using Microsoft.Maui.Controls;
using SkiaSharp;
using SkiaSharp.Views.Maui;
using SkiaSharp.Views.Maui.Controls;
using System;

namespace MauiApp1;

public partial class HomePage : ContentPage
{
    private DateTime _currentDate = DateTime.Today;
    private DateTime _selectedDate = DateTime.Today;

    public HomePage()
    {
        InitializeComponent();
        UpdateMonthYearLabels();
        GenerateMonthButtons();
        GenerateYearButtons();
    }

    private void CalendarIcon_Tap(object sender, EventArgs e)
    {
        CalendarPopup.IsVisible = true;
        CalendarCanvas.InvalidateSurface();
    }

    private void PrevButton_Tap(object sender, EventArgs e)
    {
        _currentDate = _currentDate.AddMonths(-1);
        UpdateMonthYearLabels();
        CalendarCanvas.InvalidateSurface();
    }

    private void NextButton_Tap(object sender, EventArgs e)
    {
        _currentDate = _currentDate.AddMonths(1);
        UpdateMonthYearLabels();
        CalendarCanvas.InvalidateSurface();
    }

    private void CloseButton_Click(object sender, EventArgs e)
    {
        CalendarPopup.IsVisible = false;
        MonthSelectionPopup.IsVisible = false;
        YearSelectionPopup.IsVisible = false;
    }

    private void OkButton_Click(object sender, EventArgs e)
    {
        SelectedDateLabel.Text = $"Selected Date: {_selectedDate:dd/MM/yy}";
        CalendarPopup.IsVisible = false;
    }

    private void UpdateMonthYearLabels()
    {
        MonthLabel.Text = _currentDate.ToString("MMMM");
        YearLabel.Text = _currentDate.ToString("yyyy");
    }

    private void MonthLabel_Tap(object sender, EventArgs e)
    {
        MonthSelectionPopup.IsVisible = !MonthSelectionPopup.IsVisible;
        YearSelectionPopup.IsVisible = false;
    }

    private void YearLabel_Tap(object sender, EventArgs e)
    {
        YearSelectionPopup.IsVisible = !YearSelectionPopup.IsVisible;
        MonthSelectionPopup.IsVisible = false;
    }

    private void GenerateMonthButtons()
    {
        var monthStack = (StackLayout)MonthSelectionPopup.Content;
        monthStack.Children.Clear();

        for (int i = 1; i <= 12; i++)
        {
            var button = new Button
            {
                Text = new DateTime(2000, i, 1).ToString("MMMM"),
                BackgroundColor = Colors.LightGray,
                TextColor = Colors.Black
            };
            button.Clicked += (s, e) =>
            {
                _currentDate = new DateTime(_currentDate.Year, i, 1);
                UpdateMonthYearLabels();
                CalendarCanvas.InvalidateSurface();
                MonthSelectionPopup.IsVisible = false;
            };
            monthStack.Children.Add(button);
        }
    }

    private void GenerateYearButtons()
    {
        var yearStack = (StackLayout)YearSelectionPopup.Content;
        yearStack.Children.Clear();

        for (int year = 1900; year <= 3000; year++)
        {
            var button = new Button
            {
                Text = year.ToString(),
                BackgroundColor = Colors.LightGray,
                TextColor = Colors.Black
            };
            button.Clicked += (s, e) =>
            {
                _currentDate = new DateTime(year, _currentDate.Month, 1);
                UpdateMonthYearLabels();
                CalendarCanvas.InvalidateSurface();
                YearSelectionPopup.IsVisible = false;
            };
            yearStack.Children.Add(button);
        }
    }

    private void CalendarCanvas_PaintSurface(object sender, SKPaintSurfaceEventArgs e)
    {
        var canvas = e.Surface.Canvas;
        canvas.Clear(SKColors.WhiteSmoke);

        var shadowPaint = new SKPaint
        {
            Style = SKPaintStyle.Fill,
            Color = SKColors.Gray.WithAlpha(100),
            MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, 5),
            IsAntialias = true
        };

        var circlePaint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            StrokeWidth = 2,
            Color = SKColors.Black,
            IsAntialias = true
        };

        var fillPaint = new SKPaint
        {
            Style = SKPaintStyle.Fill,
            Color = SKColors.WhiteSmoke,
            IsAntialias = true
        };

        var textPaint = new SKPaint
        {
            TextSize = 20,
            IsAntialias = true,
            TextAlign = SKTextAlign.Center,
            Color = SKColors.Black,
            Typeface = SKTypeface.Default
        };

        var daysInMonth = DateTime.DaysInMonth(_currentDate.Year, _currentDate.Month);
        var firstDay = new DateTime(_currentDate.Year, _currentDate.Month, 1).DayOfWeek;
        int startOffset = (int)firstDay;

        float cellWidth = e.Info.Width / 7f;
        float cellHeight = e.Info.Height / 6f;
        float circleRadius = 20;

        int dayCounter = 1;
        for (int row = 0; row < 6; row++)
        {
            for (int col = 0; col < 7; col++)
            {
                if ((row == 0 && col < startOffset) || dayCounter > daysInMonth)
                    continue;

                float centerX = col * cellWidth + cellWidth / 2;
                float centerY = row * cellHeight + cellHeight / 2;

                var date = new DateTime(_currentDate.Year, _currentDate.Month, dayCounter);

                if (date == DateTime.Today)
                    fillPaint.Color = SKColors.Green;
                else if (_selectedDate == date)
                    fillPaint.Color = SKColors.Red;
                else
                    fillPaint.Color = SKColors.WhiteSmoke;

                canvas.DrawCircle(centerX, centerY - 5, circleRadius + 3, shadowPaint);
                canvas.DrawCircle(centerX, centerY - 5, circleRadius, fillPaint);
                canvas.DrawCircle(centerX, centerY - 5, circleRadius, circlePaint);

                canvas.DrawText(dayCounter.ToString(), centerX, centerY + 10, textPaint);
                dayCounter++;
            }
        }
    }

    private void CalendarCanvas_Touch(object sender, SKTouchEventArgs e)
    {
        if (e.ActionType == SKTouchAction.Released)
        {
            var canvasWidth = ((SKCanvasView)sender).CanvasSize.Width;
            var canvasHeight = ((SKCanvasView)sender).CanvasSize.Height;

            float cellWidth = canvasWidth / 7f;
            float cellHeight = canvasHeight / 6f;

            int col = (int)(e.Location.X / cellWidth);
            int row = (int)(e.Location.Y / cellHeight);

            var firstDay = new DateTime(_currentDate.Year, _currentDate.Month, 1).DayOfWeek;
            int startOffset = (int)firstDay;
            int selectedDay = row * 7 + col - startOffset + 1;

            if (selectedDay >= 1 && selectedDay <= DateTime.DaysInMonth(_currentDate.Year, _currentDate.Month))
            {
                _selectedDate = new DateTime(_currentDate.Year, _currentDate.Month, selectedDay);
                CalendarCanvas.InvalidateSurface();
            }
        }
        e.Handled = true;
    }
}


# Entry Design 
https://youtu.be/QUHSdp6Z_iI?feature=shared
