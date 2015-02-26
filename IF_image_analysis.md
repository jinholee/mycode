% mycode

clear all; close all;

imG01=imread('mda231(k)_0h_1c1.tif'); % image tif file c1;Green
imR01=imread('mda231(k)_0h_1c2.tif'); % c2; Red
imB01=imread('mda231(k)_0h_1c3.tif'); % c3; Blue
figure(1);
imagesc(imB01);
figure(2);
imagesc(imG01);
figure(3);
imagesc(imR01);

thB01=0.9*graythresh(uint8(imB01));  % auto-detect threshold
bwB01=im2bw(uint8(imB01),thB01);  % convert to BW image based on thr
figure;imagesc(bwB01);colormap(gray);
bwB01=bwmorph(bwB01,'clean');
bwB01=imfill(bwB01,'holes');
bwB01=bwmorph(bwB01,'thicken',5);
bwB01=bwmorph(bwB01,'close');
bwB01=imfill(bwB01,'holes');
figure;imagesc(bwB01);colormap(gray);

cells=bwconncomp(bwB01,8);    % detect connected components
cmat=labelmatrix(cells);     % label components
prettyplot=label2rgb(cmat, @spring, 'c', 'shuffle');
figure;imagesc(prettyplot);

cprop=regionprops(cells,'all');

for c=1:length(cprop)
    if cprop(c).Area>100
        pix=cprop(c).PixelList;
        for p=1:size(pix,1)
            idx=pix(p,2);
            idy=pix(p,1);
            Gpix(c,p)=imG01(idx,idy);
            Rpix(c,p)=imR01(idx,idy);
        end;
        figure;
        plot(Gpix(c,:),Rpix(c,:),'k.');
        xlabel('Green pixel intensity');ylabel('Red pixel intensity');
        ttext=sprintf('cell #%d',c);
        title(ttext);
        aveR(c)=mean(Gpix(c,:));
        aveB(c)=mean(Rpix(c,:));
    end;
end;

figure;
plot(aveG,aveR,'o','LineWidth',2);
xlabel('average Green intensity');ylabel('average Red intensity');
