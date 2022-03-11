---
layout: post
title: Image Processing 
subtitle : resize(), flip(), getAffineTransform(), warpAffine()를 사용해 이미지 크기, 각도, 대칭 변환 등을 다룬다. 
tags: [OpenCV, Colab]
author: Rinda Kim
comments : True
---

본 글에 있는 내용은 개인적인 공부 기록용이며 유튜브의 이수안컴퓨터연구소 님이 올려주신 'OpenCV 한번에 끝내기' 과정을 따라하고 있습니다. 
그 외에 헷갈렸던 부분이나 추가적인 정보들을 찾아서 기록하고 있습니다. 
https://youtu.be/XiwA10RfbDk

<br>

<br>

<h2>1. Resize</h2>
<p>이미지의 사이즈 변경을 위해선 픽셀 사이의 값을 결정해주어야 한다. 
<br>
- 보간법(Interpolation method)
<br>
  실질적 변수 x의 함수f(x)의 모양은 미지이지나 어떤 간격을 가지는 2개 이상의 함수값이 알려져 있을 경우, 그 사이의 임의의 x에 대한 함수값을 추정하는 것. <br>
  사이즈를 줄일 때 : 'cv2. INTER_AREA' <br>
  사이즈를 늘릴 때 : 'cv2.INTER_CUBIC', 'cv2.INTER_LINEAR' <br>
  cv2.resize(img,Mask,fx=a, fy=b, interpolation=cv2.INTER_AREA)
</p>
{% highlight html %}
!wget -O moon.jpg https://cdn.pixabay.com/photo/2021/06/26/06/52/moon-6365467_960_720.jpg
image=cv2.imread('/content/moon.jpg')
print(image.shape)
{% endhighlight %}
>> (640, 960, 3)
{% highlight html %}
height, width = image.shape[:2]
shrink=cv2.resize(image,None,fx=0.5, fy=0.5, interpolation=cv2.INTER_AREA) //이미지 사이즈를 50%, 50%씩 줄여 2배 작게 만듦 
zoom1=cv2.resize(image, (width*2, height*2), interpolation=cv2.INTER_CUBIC) // 이미지 x,y 축을 2배로 하여 2배 커짐 
zoom2=cv2.resize(image,None, fx=2, fy=2, interpolation=cv2.INTER_CUBIC)
{% endhighlight %}

<h2>2. Translation</h2>
{% highlight html %}
<h1>This is heading 1</h1>
<h2>This is heading 2</h2>
<h3>This is heading 3</h3>
<h4>This is heading 4</h4>
<h5>This is heading 5</h5>
<h6>This is heading 6</h6>
{% endhighlight %}
<h1>This is heading 1</h1>
<h2>This is heading 2</h2>
<h3>This is heading 3</h3>
<h4>This is heading 4</h4>
<h5>This is heading 5</h5>
<h6>This is heading 6</h6>

<br>

<p>This is normal text - <b>and this is bold text</b>.</p>

<br>

<h2>3. Rotate</h2>
<h3>a. unordered list</h3>
{% highlight html %}
- Coffee
- Tea
- Milk
{% endhighlight %}
- Coffee
- Tea
- Milk

<h3>b. ordered list</h3>
{% highlight html %}
1. Coffee
2. Tea
3. Milk
{% endhighlight %}
1. Coffee
2. Tea
3. Milk

<br>

<h2>4. Flip</h2>
{% highlight html %}
[naye0ng's blog](https://naye0ng.github.io)
{% endhighlight %}
[naye0ng's blog](https://naye0ng.github.io)

<br>

<h2>5. Affine Transformation</h2>
선의 평행선은 유지하면서 이미지를 변환하는 작업. 
이동, 확대, Scale, 반전까지 포함된 변환이다.
Affine 변환을 위해선 3개의 점이 필요하다. 행렬변환을 사용하는데 이에 대한 개념은 본 블로그를 참고 했다. 
https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=wndrlf2003&logNo=221547082953
{% highlight html %}
rows, cols, ch = image.shape

pts1=np.float32([[200,100], [400,100], [200,200]])
pts2=np.float32([[200,300], [400,200], [200,400]])

cv2.circle(image, (200,100), 10, (255,0,0), -1)
cv2.circle(image, (400,100), 10, (0,255,0), -1)
cv2.circle(image, (200,200), 10, (0,0,255), -1)
# pts를 육안으로 확인하기 위해 각각 다른 색으로 표시를 해두었다. 

M=cv2.getAffineTransform(pts1, pts2)

dst=cv2.warpAffine(image, M, (cols, rows))
{% endhighlight %}

<br>

<h2>6. Perspective Transformation</h2>
Perspective(원근법) 변환으로, 직선의 성질만 유지되고 선의 평행성은 유지가 되지 않는 변환이다.
실습 자료인 기차길은 서로 평행하지만 원근변환을 거치면 평행성은 유지되지 못하고 하나의 점에서 만나는 것처럼 보인다. 물론 반대로 멀어지게 하는 것도 가능하다.
4개의 Poin의 Input값과 이동할 Output Point가 필요함 
'cv2.getPerspectiveTransform()'
'cv2.warpPerspective()' 함수에 변환행렬값을 적용하여 최종 결과 이미지를 얻을 수 있다. 

* 'np_float32()' 는 부동소수형(실수형) 자료형이다. 
* 'abs()'는 파이썬에서 절대값을 구하는 함수이다. 

{% highlight html %}
| Header 1  | Header 2 | Header 3 |
| :------- | :-------: | -------: |
| Content 1  | Content 2 | Content 3 |
| Content 1  | Content 2 | Content 3 |
{% endhighlight %}
