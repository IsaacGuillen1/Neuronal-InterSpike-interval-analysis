% Code to analyze Cerebellar Purkinje cell interspike intervals.

Measurement of the spontaneous firing rate (Hz) and the coefficient of variation (CV) of the inter
spike intervals (ISIs) to characterize the temporal structure of
spike trains. The analysis was done over a 2-second duration of spontaneous firing recordings sampled at 10 kHz. Recordings were
obtained across lobules of cerebellar vermis and paravermis
slices.

% Figure #1: Voltage trace showing the selected segment to analyze (in black)
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

%% Figure #2: 2-second voltage trace analysis
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

Table Results:

1×5 table

    ISI_Mean_ms    ISI_SD       CV       Total_APs1    SpikeRate_Hz
    ___________    ______    ________    __________    ____________

      23.707       1.6956    0.071524        42           42.181   

