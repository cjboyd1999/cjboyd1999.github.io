---
title: "About Me"
permalink: /about/
---
<style>
* {
-webkit-box-sizing: border-box;
-moz-box-sizing: border-box;
box-sizing: border-box;
outline: none;
}

/* body {
margin: 0;
padding: 30px 0;
font-family: 'Roboto', sans-serif;
background: #F1F2F6;
} */

/* h1 {
text-align: center;
font-weight: 300;
color: #777
}

h1 span {
font-weight: 600;
} */

.container {
width: 80%;
padding: 50px 0;
margin: 50px auto;
position: relative;
overflow: hidden;
}

.container:before {
content: '';
position: absolute;
top: 0;
left: 50%;
margin-left: -1px;
width: 2px;
height: 100%;
background: #28AE90;
z-index: 1
}

.timeline-block {
width: -webkit-calc(50% + 8px);
width: -moz-calc(50% + 8px);
width: calc(50% + 8px);
display: -webkit-box;
display: -webkit-flex;
display: -moz-box;
display: flex;
-webkit-box-pack: justify;
-webkit-justify-content: space-between;
-moz-box-pack: justify;
justify-content: space-between;
clear: both;
}

.timeline-block-right {
float: right;
}

.timeline-block-left {
float: left;
direction: rtl
}

.marker {
width: 16px;
height: 16px;
border-radius: 50%;
border: 2px solid #F5F7FA;
background: #225266;
margin-top: 10px;
z-index: 9999
}

.timeline-content {
width: 95%;
padding: 0 15px;
color: #F5F7FA
}

.timeline-content h3 {
margin-top: 5px;
margin-bottom: 5px;
font-size: 25px;
font-weight: 500
}

.timeline-content span {
font-size: 15px;
color: #F5F7FA;
}

.timeline-content p {
font-size: 14px;
line-height: 1.5em;
word-spacing: 1px;
color: #F5F7FA;
}


@media screen and (max-width: 768px) {
.container:before {
    left: 8px;
    width: 2px;
}
.timeline-block {
    width: 100%;
    margin-bottom: 30px;
}
.timeline-block-right {
    float: none;
}

.timeline-block-left {
    float: none;
    direction: ltr;
}
}
</style>

<div class="container">

<div class="timeline-block timeline-block-right">
    <div class="marker"></div>
    <div class="timeline-content">
        <h3>First Year</h3>
        <span>Some work experience</span>
        <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate.</p>
    </div>
</div>

<div class="timeline-block timeline-block-left">
    <div class="marker"></div>
    <div class="timeline-content">
        <h3>Seconed Year</h3>
        <span>Some work experience</span>
        <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate.</p>
    </div>
</div>

<div class="timeline-block timeline-block-right">
    <div class="marker"></div>
    <div class="timeline-content">
        <h3>Third Year</h3>
        <span>Some work experience</span>
        <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate.</p>
    </div>
</div>

<div class="timeline-block timeline-block-left">
    <div class="marker"></div>
    <div class="timeline-content">
        <h3>Fourth Year</h3>
        <span>Some work experience</span>
        <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate.</p>
    </div>
</div>

<div class="timeline-block timeline-block-right">
    <div class="marker"></div>
    <div class="timeline-content">
        <h3>Fifth Year</h3>
        <span>Some work experience</span>
        <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate.</p>
    </div>
</div>
</div>

## My Studies
I completed my studies for a Bachelor of Cyber Security at Deakin University in Q4 2020. With a Weighted Average Mark (WAM) of 77.7, I will be graduating with Distinction in February 2021. As such, I am currently looking for employment within the cyber security field.

Throughout my studies I have been primarily interested in penetration testing and cyber security engineering. In my downtime, you can find me doing software development both for learning and as a side hobby or playing video games.

<img style="float: right;" src="{{ site.url }}{{ site.baseurl }}/images/ceh-logo.jpg" alt="Certified Ethical Hacker Logo" width="182" height="136">

During my studies so far, I was fortunate enough to undertake the Certified Ethical Hacker exam. As a result, I am holding a Certified Ethical Hacker v10 certificate which is valid until 2023. My certification number is ECC2457893160 and can be verified [here](https://aspen.eccouncil.org/Verify).