function varargout = FruitDetection(varargin)
% FRUITDETECTION MATLAB code for FruitDetection.fig
%      FRUITDETECTION, by itself, creates a new FRUITDETECTION or raises the existing
%      singleton*.
%
%      H = FRUITDETECTION returns the handle to a new FRUITDETECTION or the handle to
%      the existing singleton*.
%
%      FRUITDETECTION('CALLBACK',hObject,eventData,handles,...) calls the local
%      function named CALLBACK in FRUITDETECTION.M with the given input arguments.
%
%      FRUITDETECTION('Property','Value',...) creates a new FRUITDETECTION or raises the
%      existing singleton*.  Starting from the left, property value pairs are
%      applied to the GUI before FruitDetection_OpeningFcn gets called.  An
%      unrecognized property name or invalid value makes property application
%      stop.  All inputs are passed to FruitDetection_OpeningFcn via varargin.
%
%      *See GUI Options on GUIDE's Tools menu.  Choose "GUI allows only one
%      instance to run (singleton)".
%
% See also: GUIDE, GUIDATA, GUIHANDLES

% Edit the above text to modify the response to help FruitDetection

% Last Modified by GUIDE v2.5 22-Jan-2022 05:08:54

% Begin initialization code - DO NOT EDIT
gui_Singleton = 1;
gui_State = struct('gui_Name',       mfilename, ...
                   'gui_Singleton',  gui_Singleton, ...
                   'gui_OpeningFcn', @FruitDetection_OpeningFcn, ...
                   'gui_OutputFcn',  @FruitDetection_OutputFcn, ...
                   'gui_LayoutFcn',  [] , ...
                   'gui_Callback',   []);
if nargin && ischar(varargin{1})
    gui_State.gui_Callback = str2func(varargin{1});
end

if nargout
    [varargout{1:nargout}] = gui_mainfcn(gui_State, varargin{:});
else
    gui_mainfcn(gui_State, varargin{:});
end
% End initialization code - DO NOT EDIT


% --- Executes just before FruitDetection is made visible.
function FruitDetection_OpeningFcn(hObject, eventdata, handles, varargin)
% This function has no output args, see OutputFcn.
% hObject    handle to figure
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
% varargin   command line arguments to FruitDetection (see VARARGIN)

% Choose default command line output for FruitDetection
handles.output = hObject;
handles.q=1;
% Update handles structure
guidata(hObject, handles);

% UIWAIT makes FruitDetection wait for user response (see UIRESUME)
% uiwait(handles.figure1);


% --- Outputs from this function are returned to the command line.
function varargout = FruitDetection_OutputFcn(hObject, eventdata, handles) 
% varargout  cell array for returning output args (see VARARGOUT);
% hObject    handle to figure
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)

% Get default command line output from handles structure
varargout{1} = handles.output;


% --- Executes on button press in pushbutton1.
function pushbutton1_Callback(hObject, eventdata, handles)
% hObject    handle to pushbutton1 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
clc
[filename, pathname] = uigetfile({'*.*';'*.bmp';'*.jpg';'*.png'}, 'Pick a Fruit Image File');
I = imread([pathname,filename]);
I2 = imresize(I,[300,400]);
axes(handles.axes1);
imshow(I2);
title('\color{white}Input Image');
handles.ImgData1 = I;
guidata(hObject,handles);


% --- Executes on button press in pushbutton2.
function pushbutton2_Callback(hObject, eventdata, handles)
% hObject    handle to pushbutton2 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
I3 = handles.ImgData1;
I4 = imadjust(I3,stretchlim(I3));
I5 = imresize(I4,[300,400]);
axes(handles.axes2);
imshow(I5);title('\color{white}Enhanced Image');
handles.ImgData2 = I4;
guidata(hObject,handles);


% --- Executes on button press in pushbutton3.
function pushbutton3_Callback(hObject, eventdata, handles)
% hObject    handle to pushbutton3 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
I6 = handles.ImgData2;
I = I6;

%% Extract Features

cform = makecform('srgb2lab');
lab_he = applycform(I,cform);
%Classify the colors in a*b* colorspace using K means clustering.
ab = double(lab_he(:,:,2:3));
nrows = size(ab,1);
ncols = size(ab,2);
ab = reshape(ab,nrows*ncols,2);
nColors = 3;
[cluster_idx, ~] = kmeans(ab,nColors,'distance','sqEuclidean', ...
                                      'Replicates',3);
pixel_labels = reshape(cluster_idx,nrows,ncols);
segmented_images = cell(1,4);
rgb_label = repmat(pixel_labels,[1,1,3]);
for k = 1:nColors
    colors = I;
    colors(rgb_label ~= k) = 0;
    segmented_images{k} = colors;
end

figure,subplot(2,3,2);
imshow(I);
title('Original Image'); 
subplot(2,3,4);imshow(segmented_images{1});title('Cluster 1'); 
subplot(2,3,5);imshow(segmented_images{2});title('Cluster 2');
subplot(2,3,6);imshow(segmented_images{3});title('Cluster 3');
% Feature Extraction
pause(1)
x = inputdlg('Enter the Cluster Number:');
i = str2double(x);
seg_img = segmented_images{i};
if ndims(seg_img) == 3
   img = rgb2gray(seg_img);
end

% Evaluate the disease affected area
black = imbinarize(seg_img,graythresh(seg_img));
m = size(seg_img,1);
n = size(seg_img,2);

zero_image = zeros(m,n); 
%G = imoverlay(zero_image,seg_img,[1 0 0]);

cc = bwconncomp(seg_img,6);
diseasedata = regionprops(cc,'basic');
A1 = diseasedata.Area;
fprintf('Area of the affected region is : %g\n',A1);

I_black = imbinarize(I,graythresh(I));
kk = bwconncomp(I,6);
appledata = regionprops(kk,'basic');
A2 = appledata.Area;
fprintf('Total Fruit area is : %g\n',A2);

%Affected_Area = 1-(A1/A2);
Affected_Area = (A1/A2);
if Affected_Area < 1
    Affected_Area = Affected_Area+0.15;
end
fprintf('Affected Area is: %g%%',(Affected_Area*100))
Affect = Affected_Area*100;
% Create the Gray Level Cooccurance Matrices (GLCMs)
glcms = graycomatrix(img);

% Derive Statistics from GLCM
stats = graycoprops(glcms,'Contrast Correlation Energy Homogeneity');
Contrast = stats.Contrast;
Correlation = stats.Correlation;
Energy = stats.Energy;
Homogeneity = stats.Homogeneity;
Mean = mean2(seg_img);
Standard_Deviation = std2(seg_img);
Entropy = entropy(seg_img);
RMS = mean2(rms(seg_img));
Variance = mean2(var(double(seg_img)));
a = sum(double(seg_img(:)));
Smoothness = 1-(1/(1+a));
Kurtosis = kurtosis(double(seg_img(:)));
Skewness = skewness(double(seg_img(:)));
m = size(seg_img,1);
n = size(seg_img,2);
in_diff = 0;
for i = 1:m
    for j = 1:n
        temp = seg_img(i,j)./(1+(i-j).^2);
        in_diff = in_diff+temp;
    end
end
IDM = double(in_diff);
fruit_feature = [Contrast,Correlation,Energy,Homogeneity, Mean, Standard_Deviation, Entropy, RMS, Variance, Smoothness, Kurtosis, Skewness, IDM];
%fn='Training_dataset.xlsx';
%t=strcat('A',int2str(handles.q));
%xlswrite(fn,fruit_feature,1,t);
%handles.q=handles.q+1;
I7 = imresize(seg_img,[300,400]);
axes(handles.axes3);
imshow(I7);title('\color{white}Segmented Image');
handles.ImgData3 = fruit_feature;
handles.ImgData4= Affect;
guidata(hObject,handles);


% --- Executes on button press in pushbutton4.
function pushbutton4_Callback(hObject, eventdata, handles)
% hObject    handle to pushbutton4 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)

I8 = handles.ImgData1;
I9 = imresize(I8,[300,400]);
axes(handles.axes4);
type = 'a'; 
[L, Centers] = imsegkmeans(I9, 3);
B = labeloverlay(I9,L);
rgbImage = B;
%{
% Display the original image.
subplot(4, 5, 1);
imshow(rgbImage);
title('Original Image(Potato)');
%}
% Split the original image into color bands.
redBand = rgbImage(:,:, 1);
greenBand = rgbImage(:,:, 2);
blueBand = rgbImage(:,:, 3);
% Threshold each color band.
redthreshold = 100;
greenThreshold = 140;
blueThreshold = 170;
redMask = (redBand < redthreshold);
greenMask = (greenBand > greenThreshold);
blueMask = (blueBand < blueThreshold);
% Combine the masks to find where all 3 are "true."
damagedAreasMask = uint8(redMask & greenMask & blueMask);
%subplot(4, 5, 2);
imshow(damagedAreasMask, []);
title('\color{white}Thresholded Image');
guidata(hObject,handles);


function edit1_Callback(hObject, eventdata, handles)
% hObject    handle to edit1 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)

% Hints: get(hObject,'String') returns contents of edit1 as text
%        str2double(get(hObject,'String')) returns contents of edit1 as a double


% --- Executes during object creation, after setting all properties.
function edit1_CreateFcn(hObject, eventdata, handles)
% hObject    handle to edit1 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    empty - handles not created until after all CreateFcns called

% Hint: edit controls usually have a white background on Windows.
%       See ISPC and COMPUTER.
if ispc && isequal(get(hObject,'BackgroundColor'), get(0,'defaultUicontrolBackgroundColor'))
    set(hObject,'BackgroundColor','white');
end


% --- Executes on button press in pushbutton5.
function pushbutton5_Callback(hObject, eventdata, handles)
% hObject    handle to pushbutton5 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)

set(handles.edit1,'string',(handles.ImgData4));

