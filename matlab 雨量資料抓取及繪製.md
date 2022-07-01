# 20210104中壢測站 時雨量
----

```

%--------------------------------------------------------------------------
%   Name: About_me_window 
%   Copyright:  
%   Author: 許芯慈
%   Version: v20220429a
%   Description: 時雨量長條圖
%   Advisor:陳建志教授
%--------------------------------------------------------------------------

clear;clc;close all
%--------------------------------------------------------------------------
% 繪製20210107 中壢測站之時雨量長條圖
% 先用爬蟲程式碼將資料從觀測站下載之後整理成.mat檔案，並且繪製
% 這裡用的程式碼需要將.mat檔案放到同一層資料夾或是空白工作室

% 一天為24小時，故設定好x軸是1~24的變數
onehour_range= 1:24;
% 測站名稱(此處為觀測站資料所設定的代號)
station_id='C0C700';
% 目標檔案之名稱
filename = '20210107_C0C700.mat';
delimiterIn = ' ';
headerlinesIn = 1;
% 將目標檔案輸入進程式中
weather_onehour = importdata(filename,delimiterIn,headerlinesIn);
% 運用for迴圈，將資料中的字串(str)轉成數值(num)，此處運用到str2num
for i_onehour_range = 1:24
    weather_onehour.Data=strrep(weather_onehour.Data,'T','0.0');
    target_onehour_data(i_onehour_range) = str2num(weather_onehour.Data{i_onehour_range,11});   % 我們想要的雨量資料是位在資料中的第11行
    
end

figname=[station_id,'測站'];
figure('NumberTitle', 'off', 'Name',figname);
% 檔案輸出，繪製1~24時的雨量長條圖
bar(onehour_range,target_onehour_data);
title('時累積降水量(mm)')
set(gca,'XTick',1:1:24);
xlabel('時間 (hr)');	% x 軸的說明文字
ylabel('雨量 (mm)');	% y 軸的說明文字

%--------------------------------------------------------------------------

```


```

S = sum(B) ;
disp(S) ;

```

# 2021年中壢測站，一月份日雨量
----

```
%--------------------------------------------------------------------------
%   Name: About_me_window 
%   Copyright:  
%   Author: 許芯慈
%   Version: v20220501a
%   Description: 日雨量長條圖
%   Advisor:陳建志教授
%--------------------------------------------------------------------------

clear;clc;close all
%--------------------------------------------------------------------------
% 繪製2021年1月份 中壢測站之日雨量長條圖
% 先用爬蟲程式碼將資料從觀測站下載之後整理成.mat檔案，並且繪製

% 測站名稱(此處為觀測站資料所設定的代號)
station_id='C0C700';
station_name='中壢';
%目標年
target_year='2021';
    start_date_str=[target_year,'-01-01'];    % 目標起始日期
    end_date_str=[target_year,'-01-31'];      % 目標結束日期
    % 設定輸出的資料中，變數的名稱，有助於得知輸出的起始和結束日期是否和自己預期的相同
    Target_Weathers.StationID=station_id;
    Target_Weathers.StationName=station_name;
    Target_Weathers.DateFrom=start_date_str;
    Target_Weathers.DateTo=end_date_str;

% 將時雨量加總，算出日雨量並輸出成長條圖

index=0;
% 在輸出的資料中加上新的變數--DataHeader(標題)
Target_Weathers.OneDay.DataHeader={'DayNumber_From','DayNumber_To','日累積降水量(mm)'};
% 此處用到datenum將輸入的起訖日期轉換成"天"(距離西元0年1月0日的天數)，便於迴圈的變換
for i_datenumber=datenum(start_date_str):datenum(end_date_str)
        % 把轉換成秒的起訖日期，再次轉換回西元日期(字串)
        date_str=datestr(i_datenumber,'yyyymmdd');
        % 設定檔案路徑(根據需要變更)
        mat_file_name=[station_id,'\',target_year,'\',date_str(5:6),'\',date_str,'_',station_id,'.mat'];
        % 如果存在同樣的檔案(名稱)，表示先前下載的資料存在對的路徑
        if (exist(mat_file_name,'file')==2)
            % 將轉換成秒的日期輸入進Target_Weathers.OneDay.Data中的第1行，第2行的資料則是
            index=index+1;
            Target_Weathers.OneDay.Data(index,1)=i_datenumber;
            Target_Weathers.OneDay.Data(index,2)=i_datenumber-1/24/60;  % 將天數轉換成分
            % 輸入目標檔案
            temp_data=load(mat_file_name);
            
            % 雨量長條圖
            temp_data2=temp_data.Weather.Data(:,11);     % 我們想要的雨量資料是位在資料中的第11行
            % 觀測站所標示的 T 為微量的意思，此處將之設定為0.0
            temp_data2=strrep(temp_data2,'T','0.0');
            % 將資料中的字串(str)轉成double(雙經度浮點型，也是數值的一種，故可以放入矩陣)，此處運用到str2double
            temp_data2=str2double(temp_data2);
            % 如果24小時有8小時都NaN就當作整天NaN，如果8小時以下就拿有數值的算總和
            if (sum(isnan(temp_data2)) >= 8)
                Target_Weathers.OneDay.Data(i_datenumber-datenum(start_date_str)+1,3)=NaN;    % i_datenumber-datenum(start_date_str)+1 為相對應的日期資料
            % 如果一個檔案的資料矩陣有24列，表示有順利讀取到24小時的雨量資料，此時可以繼續進行程式
            elseif (length(temp_data2) == 24)
                % nan的資料視為空矩陣
                temp_data2(isnan(temp_data2))=[];
                %  判斷temp_data2數列是否為空，如果是，則將sum(temp_data2)放入空格中
                if ~isempty(temp_data2)
                    Target_Weathers.OneDay.Data(i_datenumber-datenum(start_date_str)+1,3)=sum(temp_data2);      % 將時雨量累積，得出當日的總日雨量
                else
                    Target_Weathers.OneDay.Data(i_datenumber-datenum(start_date_str)+1,3)=NaN;  
                end
            else
                Target_Weathers.OneDay.Data(i_datenumber-datenum(start_date_str)+1,3)=NaN;            
            end

        end

end

figname=[station_id,'測站'];
figure('NumberTitle', 'off', 'Name',figname);
x=1:31;
bar(x,Target_Weathers.OneDay.Data(:,3));
title('日累積降水量(mm)')
set(gca,'XTick',1:1:31);
xlabel('時間 (日)');	% x 軸的說明文字
ylabel('雨量 (mm)');	% y 軸的說明文字


```
# 2022年 中壢測站月雨量

```

%--------------------------------------------------------------------------
%   Name: About_me_window 
%   Copyright:  
%   Author: 許芯慈
%   Version: v20220523a
%   Description: 日雨量長條圖
%   Advisor:陳建志教授
%--------------------------------------------------------------------------

clear;clc;close all
%--------------------------------------------------------------------------
% 繪製2021年1月份 中壢測站之日雨量長條圖
% 先用爬蟲程式碼將資料從觀測站下載之後整理成.mat檔案，並且繪製

% 測站名稱(此處為觀測站資料所設定的代號)
station_id='C0C700';
station_name='中壢';
month_data='月雨量資料';
%目標年
target_year='2020';
    % 設定輸出的資料中，變數的名稱，有助於得知輸出的起始和結束日期是否和自己預期的相同
    Target_Weathers.StationID=station_id;
    Target_Weathers.StationName=station_name;

% 將時雨量加總，算出日雨量並輸出成長條圖

index=0;
% 在輸出的資料中加上新的變數--DataHeader(標題)
Target_Weathers.OneMonth.DataHeader={'Target_Month','月累積降水量(mm)'};
for i_month= 1:12      
        % 設定檔案路徑(根據需要變更)
        mat_file_name=[month_data,'\',station_id,'\',target_year,'\',target_year,'_',station_id,'.mat'];
        % 如果存在同樣的檔案(名稱)，表示先前下載的資料存在對的路徑
        if (exist(mat_file_name,'file')==2)
            % 將轉換成秒的日期輸入進Target_Weathers.Onemonth.Data中的第1行，第2行的資料則是
            index=index+1;
            Target_Weathers.OneMonth.Data(index,1)=i_month;
            % 輸入目標檔案
            temp_data=load(mat_file_name);
            
            % 雨量長條圖
            temp_data2=temp_data.Weather.Data(:,19);     % 我們想要的雨量資料是位在資料中的第19行
            % 觀測站所標示的 T 為微量的意思，此處將之設定為0.0
            temp_data2=strrep(temp_data2,'T','0.0');
            % 將資料中的字串(str)轉成double(雙經度浮點型，也是數值的一種，故可以放入矩陣)，此處運用到str2double
            temp_data2=str2double(temp_data2);
            % 如果12個月中有4個月都NaN就當作整天NaN，如果8小時以下就拿有數值的算總和
            if (sum(isnan(temp_data2)) >= 4)
                Target_Weathers.OneMonth.Data(i_month,2)=NaN;    
            % 如果一個檔案的資料矩陣有12列，表示有順利讀取到12個月的雨量資料，此時可以繼續進行程式
            elseif (length(temp_data2) == 12)
                % nan的資料視為空矩陣
                temp_data2(isnan(temp_data2))=[];
                %  判斷temp_data2數列是否為空，如果是，則將sum(temp_data2)放入空格中
                if ~isempty(temp_data2)
                    Target_Weathers.OneMonth.Data(i_month,2)=temp_data2(i_month,1);
                else
                    Target_Weathers.OneMonth.Data(i_month,2)=NaN;  
                end
            else
                Target_Weathers.OneMonth.Data(i_month,2)=NaN;            
            end

        end
end
figname=[station_id,'測站'];
figure('NumberTitle', 'off', 'Name',figname);
x=1:12;
bar(x,Target_Weathers.OneMonth.Data(:,2));
title('月累積降水量(mm)')
set(gca,'XTick',1:1:12);
xlabel('時間 (月)');	% x 軸的說明文字
ylabel('雨量 (mm)');	% y 軸的說明文字



```


# CWB月雨量資料抓取

```

%--------------------------------------------------------------------------
%   Name: About_me_window 
%   Copyright:  
%   Author: 許芯慈
%   Version: v20220511a
%   Description: CWB月雨量資料抓取
%   Advisor:陳建志教授
%--------------------------------------------------------------------------

clear;clc;close all
%--------------------------------------------------------------------------
% 抓取CWB之月雨量資料
station_id='C0C700';
station_name='中壢';
station_name_urlencode=urlencode(urlencode(station_name));%不知道為什麼氣象局要urlencode兩次...
target_date_str='2020';
month_data='月雨量資料';
% 建立輸出資料夾
output_folder=[month_data,'\',station_id,'\',target_date_str(1:4)];
if exist(output_folder,'dir')~=6
    mkdir(output_folder)
end
%--

% 準備輸出檔名
output_mat_file_name=[output_folder,'\',target_date_str(1:4),'_',station_id,'.mat'];
%--

Weather.StationID=station_id;
Weather.StationName=station_name;
Weather.Date=target_date_str;

 % 下載
if (exist(output_mat_file_name,'file')==2)
    disp('檔案存在,skip!')
else
    disp(target_date_str)
    temp_char = webread(['https://e-service.cwb.gov.tw/HistoryDataQuery/YearDataController.do?command=viewMain&station=',station_id,'&stname=',station_name_urlencode,'&datepicker=',target_date_str,'&altitude=151m']);
    % 確認一年有12個月
    temp_str='</tr>';
    temp_index=strfind(temp_char,temp_str);
    month_count=length(temp_index)-4;
    % 找開頭
    temp_str='<tr class="first_tr">';
    temp_start_index=strfind(temp_char,temp_str);
    % 去掉前面的
    temp_char=temp_char(temp_start_index(1):end);
    % 找結尾
    temp_str='</tr>';
    temp_end_index=strfind(temp_char,temp_str);
    temp_target_char=temp_char(1:temp_end_index(1)+length(temp_str)-1);
    % 去掉前面的
    temp_char=temp_char(temp_end_index(1)+length(temp_str):end);
    %--
    % 分析
    temp_th_start_index=strfind(temp_target_char,'<th');
    temp_th_end_index=strfind(temp_target_char,'</th>');
    if length(temp_th_start_index)==length(temp_th_end_index)
        temp_vertical_column_count=length(temp_th_start_index);
    else
        disp('錯誤!<th>與</th>數量不同!')
        temp_vertical_column_count=0;
        return
    end
    %--
    % 寫入資料標頭
    for i=1:temp_vertical_column_count
        temp_str=temp_target_char(temp_th_start_index(i):temp_th_end_index(i)-1);
        temp_str=strrep(temp_str,'<th>','');
        temp_str=strrep(temp_str,'<br>','');
        temp_str=strrep(temp_str,'<th width="10px">&nbsp;','');
        temp_str=strrep(temp_str,'<th colspan="2">','');
        temp_str=strrep(temp_str,'<th colspan="4">','');
        Weather.DataHeader1{1,i}=temp_str;
    end
    temp_str='<tr class="second_tr">';
    temp_start_index=strfind(temp_char,temp_str);
    % 去掉前面的
    temp_char=temp_char(temp_start_index(1):end);
    % 找結尾
    temp_str='</tr>';
    temp_end_index=strfind(temp_char,temp_str);
    temp_target_char=temp_char(1:temp_end_index(1)+length(temp_str)-1);
    % 去掉前面的
    temp_char=temp_char(temp_end_index(1)+length(temp_str):end);
    %--
    % 分析
    temp_th_start_index=strfind(temp_target_char,'<th');
    temp_th_end_index=strfind(temp_target_char,'</th>');
    if length(temp_th_start_index)==length(temp_th_end_index)
        temp_vertical_column_count=length(temp_th_start_index);
    else
        disp('錯誤!<th>與</th>數量不同!')
        temp_vertical_column_count=0;
        return
    end
    %--
    % 寫入資料標頭
    for i=1:temp_vertical_column_count
        temp_str=temp_target_char(temp_th_start_index(i):temp_th_end_index(i)-1);
        temp_str=strrep(temp_str,'<th>','');
        temp_str=strrep(temp_str,'<br>','');
        temp_str=strrep(temp_str,'<th width="10px">&nbsp;','');
        temp_str=strrep(temp_str,'<th colspan="2">','');
        temp_str=strrep(temp_str,'<th colspan="4">','');
        Weather.DataHeader2{1,i}=temp_str;
    end
    temp_str='<tr class="third_tr">';
    temp_start_index=strfind(temp_char,temp_str);
    % 去掉前面的
    temp_char=temp_char(temp_start_index(1):end);
    % 找結尾
    temp_str='</tr>';
    temp_end_index=strfind(temp_char,temp_str);
    temp_target_char=temp_char(1:temp_end_index(1)+length(temp_str)-1);
    % 去掉前面的
    temp_char=temp_char(temp_end_index(1)+length(temp_str):end);
    %--
    % 分析
    temp_th_start_index=strfind(temp_target_char,'<th');
    temp_th_end_index=strfind(temp_target_char,'</th>');
    if length(temp_th_start_index)==length(temp_th_end_index)
        temp_vertical_column_count=length(temp_th_start_index);
    else
        disp('錯誤!<th>與</th>數量不同!')
        temp_vertical_column_count=0;
        return
    end
    %--
    % 寫入資料標頭
    for i=1:temp_vertical_column_count
        temp_str=temp_target_char(temp_th_start_index(i):temp_th_end_index(i)-1);
        temp_str=strrep(temp_str,'<th>','');
        temp_str=strrep(temp_str,'<br>','');
        Weather.DataHeader3{1,i}=temp_str;
    end
    for i_loop=1:month_count
        % 找開頭
        temp_str='<tr>';
        temp_start_index=strfind(temp_char,temp_str);
        % 去掉前面的
        temp_char=temp_char(temp_start_index(1):end);
        % 找結尾
        temp_str='</tr>';
        temp_end_index=strfind(temp_char,temp_str);
        temp_target_char=temp_char(1:temp_end_index(1)+length(temp_str)-1);
        % 去掉前面的
        temp_char=temp_char(temp_end_index(1)+length(temp_str):end);
        %--
        % 分析
        temp_th_start_index=strfind(temp_target_char,'<td');
        temp_th_end_index=strfind(temp_target_char,'</td>');
        if length(temp_th_start_index)==length(temp_th_end_index)
            temp_vertical_column_count=length(temp_th_start_index);     
        else
            disp('錯誤!<th>與</th>數量不同!')
            temp_vertical_column_count=0;
            return
        end
        %--
        % 寫入資料標頭
        for i=1:temp_vertical_column_count  
            temp_str=temp_target_char(temp_th_start_index(i):temp_th_end_index(i)-1);
            temp_str=temp_target_char(temp_th_start_index(i):temp_th_end_index(i)-1);
            temp_str=strrep(temp_str,'<td nowrap>','');
            temp_str=strrep(temp_str,'<td>','');
            temp_str=strrep(temp_str,'&nbsp;',''); 
            Weather.Data{i_loop,i}=temp_str;
        end
        %--------------------------------------------------------------------------

    %--------------------------------------------------------------------------        
    % 存檔        
        save(output_mat_file_name,'Weather')
    %-------------------------------------------------------------------------
    end
end
disp('結束!')

```
