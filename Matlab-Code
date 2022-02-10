classdef WheatherViewApp < matlab.apps.AppBase

    % Properties that correspond to app components
    properties (Access = public)
        UIFigure                     matlab.ui.Figure
        TextArea                     matlab.ui.control.TextArea
        Insertyeartofocuson18592019EditField_2  matlab.ui.control.NumericEditField
        Insertyeartofocuson18592019EditField_2Label  matlab.ui.control.Label
        ViewingOptionsDropDownLabel  matlab.ui.control.Label
        ViewingOptionsDropDown       matlab.ui.control.DropDown
        ZoomSliderLabel              matlab.ui.control.Label
        ZoomSlider                   matlab.ui.control.Slider
        InsertButton                 matlab.ui.control.Button
        LoadFileButton               matlab.ui.control.Button
        UIAxes                       matlab.ui.control.UIAxes
    end

    
    properties (Access = private)
        Data % Description
        Years
        Average
        FocusedYear = -1
        LoadFlag=0
    end
    
    methods (Access = private)
        
        function PlotStdValues(app)
            %In this function we calculate the standar deniation values and
            %then we fill the redeion bettween (mean + std,mean -std) 
            y = app.Average; % your mean vector;
            x = app.Years';
            stdMatrix = std(app.Data');
            stdupper = app.Average' + stdMatrix; %upper limit
            stdlower = app.Average' - stdMatrix;
            plot(app.UIAxes,x, y);
            hold(app.UIAxes,'on');
            x2 = [x, fliplr(x)];
            inBetween = [stdupper, fliplr(stdlower)];
            h = fill(app.UIAxes,x2, inBetween, 'c'); %colour betwwen
            h.FaceAlpha = 0.08;
            scatter1 = scatter(app.UIAxes,x,y); 
            scatter1.MarkerFaceAlpha = .2;
            hold(app.UIAxes,'off');
        end
    end
    

    % Callbacks that handle component events
    methods (Access = private)

        % Button pushed function: LoadFileButton
        function LoadFileButtonPushed(app, event)
            %Disable matlab zooming tools
            disableDefaultInteractivity(app.UIAxes)
            app.UIAxes.Toolbar.Visible = 'off'; 
            %Allow with flag the other prosedures because we ghave data
            app.LoadFlag = 1;
            %Load data
            [file,path,~]=uigetfile('*.csv'); 
            filename = [path,file];
            
            %Toss last coloub becaouse of nan values
            RawData = readtable(filename,'ReadVariableNames',false);
            TemperatureData = RawData(1:end-1,4:15);
            years = RawData(1:end-1,3);
            average = RawData(1:end-1,16);
            
            %or we could replace all nan values with the mean of first row because we
            %have little variotion data and will not affect the result.
            %We can not have nan values because will affect further
            %calculations (prosedures in comments below)
            app.Data = table2array(TemperatureData);
            %NanValues = isnan(app.Data);
            %MeanValue = mean(mean(app.Data(1:end-1,:)));
            %app.Data(NanValues>0) = MeanValue; 
            app.Years = table2array(years);
            app.Average = table2array(average);
            %NanValues = isnan(app.Average);
            %MeanValue = mean(mean(app.Average(1:end-1,:)));
            %app.Average(NanValues>0) = MeanValue;
            
            %Plot in bar plot
            bar(app.UIAxes,app.Years,app.Data)
            legend(app.UIAxes,'Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec');
            app.TextArea.Value = "Data successfully loaded";
        
        end

        % Button pushed function: InsertButton
        function InsertButtonPushed(app, event)
            %check if we have data and in the year is in range and then
            %update focuced year to zoom in
            if app.LoadFlag == 0
                app.TextArea.Value = "Give csv file";
            else
                if((app.FocusedYear<1859) || (app.FocusedYear>2019))
                    app.TextArea.Value = "Invalid year, must be bettween 1859-2019";
    
                else
                    app.TextArea.Value = "";
                    xlim(app.UIAxes,[app.FocusedYear-40 app.FocusedYear+40])
                    app.ZoomSlider.Value = 50;
                    app.Insertyeartofocuson18592019EditField_2.Value = app.FocusedYear;
                end
            end
        end

        % Value changed function: ZoomSlider
        function ZoomSliderValueChanged(app, event)
            %Convert sliders value to zoom in an out using xlimit
            if app.LoadFlag == 0
                app.TextArea.Value = "Give csv file";
            else
                if(app.FocusedYear == -1)
                    app.TextArea.Value = "Pleaase give focuse Year first";
                else
                    value = app.ZoomSlider.Value;
                    range = (1.01 - value/100)*length(app.Years);
        
                    xlim(app.UIAxes,[app.FocusedYear-(range/2) app.FocusedYear+(range/2)])
                end
            end
        end

        % Value changed function: Insertyeartofocuson18592019EditField_2
        function Insertyeartofocuson18592019EditField_2ValueChanged(app, event)
            %Update focusedYear value 
            value = app.Insertyeartofocuson18592019EditField_2.Value;
            app.FocusedYear= value;
        end

        % Value changed function: ViewingOptionsDropDown
        function ViewingOptionsDropDownValueChanged(app, event)
            %Drop down menue to plot all deferent views using input by it
            if app.LoadFlag == 0
            app.TextArea.Value = "Give csv file";
            else
                value = app.ViewingOptionsDropDown.Value;
                if (value == "All Temperatures")
                    ylim(app.UIAxes,[0 30])
                    bar(app.UIAxes,app.Years,app.Data)
                    legend(app.UIAxes,'Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec');
                elseif(value == "Grouped by Year")
                    ylim(app.UIAxes,[17 28])
                    plot(app.UIAxes,app.Years,app.Average)
                    legend(app.UIAxes,'Graph','Temperatures');
                    hold(app.UIAxes,'on');
                    scatter1 = scatter(app.UIAxes,app.Years,app.Average); 
                    scatter1.MarkerFaceAlpha = .2;
                    hold(app.UIAxes,'off');
                else
                    ylim(app.UIAxes,[17 28])
                    PlotStdValues(app)
                    legend(app.UIAxes,'Graph','Temperatures','Std');
                end
            end
        end
    end

    % Component initialization
    methods (Access = private)

        % Create UIFigure and components
        function createComponents(app)

            % Create UIFigure and hide until all components are created
            app.UIFigure = uifigure('Visible', 'off');
            app.UIFigure.Color = [0.3608 0.349 0.3804];
            app.UIFigure.Position = [100 100 836 616];
            app.UIFigure.Name = 'MATLAB App';

            % Create UIAxes
            app.UIAxes = uiaxes(app.UIFigure);
            xlabel(app.UIAxes, 'Years')
            ylabel(app.UIAxes, 'Temperatures (Celsius)')
            zlabel(app.UIAxes, 'Z')
            app.UIAxes.FontName = 'Serif';
            app.UIAxes.XColor = [1 1 1];
            app.UIAxes.YColor = [1 1 1];
            app.UIAxes.ZColor = [1 1 1];
            app.UIAxes.Color = [0.149 0.149 0.149];
            app.UIAxes.YGrid = 'on';
            app.UIAxes.ColorOrder = [0.2314 0.8275 0.8588;0.902 0.902 0.902;0.9294 0.6941 0.1255;0.4941 0.1843 0.5569;0.4667 0.6745 0.1882;0.302 0.7451 0.9333;0.6353 0.0784 0.1843;0 0 1;1 0.0745 0.651;1 1 0.0667;0.7608 0.0627 0.4902;0.851 0.3294 0.102];
            app.UIAxes.GridColor = [1 1 1];
            app.UIAxes.MinorGridColor = [0.6353 0.0784 0.1843];
            app.UIAxes.GridAlpha = 0.5;
            app.UIAxes.Position = [23 155 794 349];

            % Create LoadFileButton
            app.LoadFileButton = uibutton(app.UIFigure, 'push');
            app.LoadFileButton.ButtonPushedFcn = createCallbackFcn(app, @LoadFileButtonPushed, true);
            app.LoadFileButton.BackgroundColor = [0.4784 0.4902 0.6784];
            app.LoadFileButton.FontColor = [1 1 1];
            app.LoadFileButton.Position = [228 36 407 40];
            app.LoadFileButton.Text = 'Load File';

            % Create InsertButton
            app.InsertButton = uibutton(app.UIFigure, 'push');
            app.InsertButton.ButtonPushedFcn = createCallbackFcn(app, @InsertButtonPushed, true);
            app.InsertButton.BackgroundColor = [0.4784 0.4902 0.6784];
            app.InsertButton.FontColor = [1 1 1];
            app.InsertButton.Position = [357 557 80 22];
            app.InsertButton.Text = 'Insert';

            % Create ZoomSlider
            app.ZoomSlider = uislider(app.UIFigure);
            app.ZoomSlider.ValueChangedFcn = createCallbackFcn(app, @ZoomSliderValueChanged, true);
            app.ZoomSlider.Position = [120 137 668 3];

            % Create ZoomSliderLabel
            app.ZoomSliderLabel = uilabel(app.UIFigure);
            app.ZoomSliderLabel.HorizontalAlignment = 'right';
            app.ZoomSliderLabel.Position = [63 128 36 22];
            app.ZoomSliderLabel.Text = 'Zoom';

            % Create ViewingOptionsDropDown
            app.ViewingOptionsDropDown = uidropdown(app.UIFigure);
            app.ViewingOptionsDropDown.Items = {'All Temperatures', 'Grouped by Year', 'Grouped by Year with standar diviation'};
            app.ViewingOptionsDropDown.ValueChangedFcn = createCallbackFcn(app, @ViewingOptionsDropDownValueChanged, true);
            app.ViewingOptionsDropDown.BackgroundColor = [0.902 0.902 0.902];
            app.ViewingOptionsDropDown.Position = [170 511 249 22];
            app.ViewingOptionsDropDown.Value = 'All Temperatures';

            % Create ViewingOptionsDropDownLabel
            app.ViewingOptionsDropDownLabel = uilabel(app.UIFigure);
            app.ViewingOptionsDropDownLabel.HorizontalAlignment = 'right';
            app.ViewingOptionsDropDownLabel.FontColor = [1 1 1];
            app.ViewingOptionsDropDownLabel.Position = [63 511 92 22];
            app.ViewingOptionsDropDownLabel.Text = 'Viewing Options';

            % Create Insertyeartofocuson18592019EditField_2Label
            app.Insertyeartofocuson18592019EditField_2Label = uilabel(app.UIFigure);
            app.Insertyeartofocuson18592019EditField_2Label.BackgroundColor = [0.3608 0.349 0.3804];
            app.Insertyeartofocuson18592019EditField_2Label.HorizontalAlignment = 'right';
            app.Insertyeartofocuson18592019EditField_2Label.FontColor = [1 1 1];
            app.Insertyeartofocuson18592019EditField_2Label.Position = [62 557 194 22];
            app.Insertyeartofocuson18592019EditField_2Label.Text = 'Insert year to focus on (1859-2019)';

            % Create Insertyeartofocuson18592019EditField_2
            app.Insertyeartofocuson18592019EditField_2 = uieditfield(app.UIFigure, 'numeric');
            app.Insertyeartofocuson18592019EditField_2.ValueChangedFcn = createCallbackFcn(app, @Insertyeartofocuson18592019EditField_2ValueChanged, true);
            app.Insertyeartofocuson18592019EditField_2.BackgroundColor = [0.902 0.902 0.902];
            app.Insertyeartofocuson18592019EditField_2.Position = [264 557 79 22];

            % Create TextArea
            app.TextArea = uitextarea(app.UIFigure);
            app.TextArea.FontSize = 14;
            app.TextArea.FontWeight = 'bold';
            app.TextArea.FontColor = [0.0588 1 1];
            app.TextArea.BackgroundColor = [0.3608 0.349 0.3804];
            app.TextArea.Enable = 'off';
            app.TextArea.Position = [456 557 261 23];

            % Show the figure after all components are created
            app.UIFigure.Visible = 'on';
        end
    end

    % App creation and deletion
    methods (Access = public)

        % Construct app
        function app = WheatherViewApp

            % Create UIFigure and components
            createComponents(app)

            % Register the app with App Designer
            registerApp(app, app.UIFigure)

            if nargout == 0
                clear app
            end
        end

        % Code that executes before app deletion
        function delete(app)

            % Delete UIFigure when app is deleted
            delete(app.UIFigure)
        end
    end
end
