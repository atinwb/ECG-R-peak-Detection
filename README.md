# ECG-R-peak-Detection
MATLAB program to detect R peaks in an ECG signal using discrete wavelet transform
load('ECG_data.mat');
yb = val(1,1:3000);
yb_norm= yb./max(yb);
yb_norm1= yb_norm-mean(yb_norm);
[a,d] = lwt(yb_norm1,'db2'); //lifting-based discrete wavelet transform; "a" is the approximation   coefficient and "d" is the detailed coefficient, “db2” refers to Daubechies 4-tap wavelet filter
[a1,d1] = lwt(a,'db2');
[a2,d2] = lwt(a1,'db2');
[a3,d3] = lwt(a2,'db2');
m1= max(d3); //maximum amplitude in level four detailed coefficient 
th1 = 0.18*m1; //Threshold calculation
D_3= abs(d3);
for i1=1:length(D_3) //QRS complex detection
	if D_3(i1)>th1
		QRS(i1)= D_3(i1);
	else
		i1=i1+1;
	end
end
maximum=0;
k=1;
j=1;
j1=1;
for i=1:length(QRS) //storing the maximum amplitude within each QRS complex detected in array “m1”
        if (maximum<QRS(i))
            maximum=QRS(i);
        end
        if (QRS(i+1)==0 && maximum~=0)
            m1(k)=maximum ;
            k=k+1;
            maximum=0;
        end
end
for k=1:length(m1) //storing the location of the maximum amplitude in array “b” as R-peaks and the maximum amplitude value in array “b1” as magnitude of R-peaks 
 for i=1:length(QRS)
        if (m1(k)==QRS(i))
            b(j)=i;
	b1(j1)=QRS;
            j=j+1;
	j1=j1+1;
        end
    end
end
l=1; 
w=1;  
for z=1:length(b) //removal of false-peaks and detecting R-peaks
    if((b(l+1)-b(z)<2))|| ((b(l+1)-b(z))<8)
		if(b1(l+1)>b1(z))
			R(w)=b(l+1);
        		l=l+1;
		else
			R(w)=b(z);
			l=l+1;
		end
    else 
        w=w+1;
        z=l+1;
        l=z;
        R(w)=b(z);
    end
end
s1=1; 
max1=0; 
s2=0; 
s3=0; 
max2=0; 
l2=1;
yb_norm_abs= abs(yb_norm1);
for s1=1:length(x) //windowing technique to detect the exact R-peak locations as R_locations
	s2=R(s1)-30;
	s3=R(s1)+30;
		for s4=s2:s3
			if(max1<yb_norm_abs)
				max1=yb_norm_abs;
				max2=s4;
			else
				R_location(l2)=max2;
				l2=l2+1;
			end
		end
end
q1=1;
for q=1:length(R_location) //RR interval calculations
    RR(q1)=R_locatiom(q+1)-R_location(q);
    q1=q1+1;
end
RRavg= mean(RR); 
RRavg_intime= RRavg/360;
HR= 60/RRavg_intime; //Heart_rate calculation
