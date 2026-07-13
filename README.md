%% Script to analyze Purkinje cell interspike-interval (p84-p98).
% Interspike-Interval protocol (10 kHz SR)
% Updated 7/13/2026 by Isaac Guillen
clear;clc;

%% 1.Select experiment & cell 
Experiment = num2str('Exp19.xlsx');      % Select experiment number
Cell = num2str('Cell 1');               % Select cell number (or spreadsheet)

RecordedCells = sheetnames(Experiment); % Return recorded cells in the experiment
  disp(['Experiment: ',Experiment]);
  disp(RecordedCells);
  disp(['Analyzing InterSpike-Interval: ', Cell]);

% 2.Import data from excel to matlab
CellData = readmatrix(Experiment,...
    'Sheet',Cell,'Range','');     % Import data from selected excel & spreadsheet 

%% Mouse Genotype & Age
GenoType= CellData(1,1);          % Return mouse genotype:'WT' or 'KO'
Age = CellData(1,2);              % Return mouse age (weeks)
if GenoType == 1
    disp('Genotype: WT mouse');
    disp(['Mouse age: ',num2str(Age),' weeks']);
    Mouse = ('WT');
elseif GenoType == 2
        disp('Genotype: KO mouse');
        disp(['Mouse Age: ',num2str(Age),' weeks']);
        Mouse = ('KO');
end

%% 3.Assign values to variables (GapFree)

% Gap free 20 seconds trace @ 0pA 
Time = CellData(3:end,1);            % x = time(ms)
x = Time./1000;                      % Convert ms to seconds
y= CellData(3:end,2);                % y1= voltage trace

% % Gap free 1 second trace
% Time1 = CellData(3:10003,1);       % x = time(ms)
% x1 = Time1./1000;                  % Convert ms to seconds
% y1= CellData(3:10003,2);           % y1= voltage trace

% Gap free 1 second window interval
[row, col]= find(x>=2 & x<=3);      % Select second/seconds to analyze                
x1 = x(row);                         % Time in seconds
y1= y(row);                          % y1= voltage trace

Time1 = x1.*1000;                    % Convert seconds to ms

%% 4.Find number of spikes (mV x ms)
baseline= -20;  % Set up baseline in y-axis to quantify spikes

size(y1,2);
[pks1,time1] = arrayfun(@(col)findpeaks(y1(:,col),Time1,...
    'MinPeakHeight',baseline),1:size(y1,2),'UniformOutput',false);
X1 = pks1';
T1 = time1';
NumAPs1 = cell2mat(X1);     % Units: mV
TimeAPs1= cell2mat (T1);    % Units: ms

Total_APs1 = size(NumAPs1,1);

%% 5.InterSpike Interval Analysis
peakInterval =diff(TimeAPs1);

% Average distance peaks (descriptive statistics)
ISI_Mean_ms =mean(diff(TimeAPs1));
ISI_SD =std(diff(TimeAPs1));
STDerror= std((TimeAPs1)./sqrt(length(TimeAPs1)))';

% Coefficient of Variation (is a measure of relative variability to its mean)
CV = (ISI_SD/ISI_Mean_ms);                     % CV measures variability in spike trains       
SpikeRate_Hz = inv(ISI_Mean_ms).*1000;     % Average Firing Rate = inv(mean)*1000 

A_Results= table(ISI_Mean_ms, ISI_SD, CV, Total_APs1, SpikeRate_Hz)

%% 6.Plots
% Figure#1: 20 seconds voltage trace
figure
plot(x,y,'Color',[0.5 0.5 0.5]);
hold on;
plot(x1,y1,'k');
xline ([x1(1,1) x1(end)],':r','linewidth',0.5);
ylabel('mV');
ylim([-70 25]);
xlim padded;
xlabel('Time (S)');
ylabel('mV');
legend(Cell,'Segment to analyze','Location','northeast','Box','off');
title([Experiment,' / ',Mouse,': ',num2str(Age),' weeks'],...
        'Purkinje cell: Spontaneous Firing');
box off;

set(findall(gcf,'-property','FontSize'),'FontSize',12);

<img width="1270" height="476" alt="Fig1" src="https://github.com/user-attachments/assets/7dc4bdad-8ea9-469a-8bc0-280f0ecb25d1" />

%% Figure#2: 1 second voltage trace analysis
figure;
subplot(3,3,[1 2 4 5 7 8]);                        % Spikes
plot(x1,y1,'color',[0.5 0.5 0.5],'linewidth',2);
xlabel('Seconds');
ylabel('mV');
yline (0,'Color',[0.5 0.5 0.5],'linewidth',1,'LineStyle','--');
ylim([-70 25]);
xlim padded;
legend(Cell,'Box','off');
title('Purkinje cell: Spontaneous Firing');
box off;

subplot(3,3,3);                                   % Count spikes
findpeaks(y1,x1,'MinPeakHeight',baseline);
xlabel('Seconds');
ylabel('mV');
yline (-20,':r','linewidth',1);
ylim([-70 29]);
xlim padded;
apstr1= sprintf('Total Spikes: %.0f',Total_APs1);
legend(apstr1,'Location','northeast','Box','off');
title('','Finding Spikes');
grid off
box off;

subplot(3,3,[6 9]);                                         
h1 =histogram(peakInterval);                
h1.FaceColor= [0.5 0.5 0.5];                    % ISI histogram
h1.EdgeAlpha = 0.4;
h1.FaceAlpha = 0.8;
h1.BinWidth = 5;                                % bin width                            
xbin = h1.BinEdges ;
yvalue = h1.Values ;
text(xbin(1:end-1),yvalue,num2str(yvalue'),...
    'vert','bottom','horiz','center'); 
xlabel('InterSpike-Interval (ms)');
ylabel('# of events');
xlim([0 100]);
xline(ISI_Mean_ms,'r-','LineWidth',1.5);
ylim auto
xticks(0:5:100);
box off

% Legend
str1= sprintf('Mean: %.1f ms',ISI_Mean_ms);
str2= sprintf('Spike Rate: %.1f Hz',SpikeRate_Hz);
apstr2= sprintf('Events: %.0f',Total_APs1);
lgd = legend(apstr2,...
    str1,'Box','off');
title(lgd,sprintf('Spiking Rate: %.1f Hz',SpikeRate_Hz));
title('','Interspike Interval Histogram');
box off;

%
sgtitle([Experiment,' / ',Mouse,': ',num2str(Age),' weeks']);
set(findall(gcf,'-property','FontSize'),'FontSize',10);
   
<img width="1698" height="690" alt="Fig2" src="https://github.com/user-attachments/assets/623a0d5f-d594-472b-9678-b8696dcc1eb4" />

disp('FInished!');
