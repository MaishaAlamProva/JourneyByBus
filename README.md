
#include <windows.h>
#include <iostream>
#include <cmath>
#include <GL/gl.h>
#include <GL/glut.h>
#include <stdbool.h>
#include <time.h>
#include <stdlib.h>

using namespace std;

const float PI = 3.14159265358979323846;


void drawSemiCircle(float x, float y, float radius, float tiltAngle) {
    int numSegments = 100; // Number of segments for smoothness
    float angleStep = M_PI / numSegments; // Angle step for semi-circle



    // Draw semi-circle as a triangle fan
    glBegin(GL_TRIANGLE_FAN);
    glColor3f(0.2f, 0.6f, 0.8f); // Semi-circle color
    glVertex2f(0.0f, 0.0f); // Center of the semi-circle

    for (int i = 0; i <= numSegments; ++i) {
        float angle = i * angleStep; // Current angle
        float vx = radius * cos(angle);
        float vy = radius * sin(angle);
        glVertex2f(vx, vy);
    }

    glEnd();
}
void drawLowerSemicircle(float cx, float cy, float radius, int segments) {

    glBegin(GL_TRIANGLE_FAN);
    glVertex2f(cx, cy);
    for (int i = 0; i <= segments; i++) {
        float angle = M_PI + M_PI * (float)i / (float)segments;
        float x = cx + radius * cos(angle);
        float y = cy + radius * sin(angle);
        glVertex2f(x, y);
    }
    glEnd();
}
void drawTiltedSemicircle(float x, float y, float radius, float angle, bool isFilled) {
    int numSegments = 100; // Number of segments for the circle


    if (isFilled) {
        // Draw filled upper semicircle
        glColor3f(0.0f, 0.0f, 1.0f); // Set color (Blue)
        glBegin(GL_POLYGON); // Filled semicircle
        for (int i = 0; i <= numSegments / 2; i++) {
            float angle = 2.0f * 3.1415926f * i / numSegments;
            float xCoord = radius * cosf(angle);
            float yCoord = radius * sinf(angle);
            glVertex2f(xCoord, yCoord);
        }
        glEnd();
    } else {
        // Draw hollow lower semicircle
        glColor3f(1.0f, 0.0f, 0.0f); // Set color (Red)
        glBegin(GL_LINE_LOOP); // Hollow semicircle
        for (int i = numSegments / 2; i <= numSegments; i++) {
            float angle = 2.0f * 3.1415926f * i / numSegments;
            float xCoord = radius * cosf(angle);
            float yCoord = radius * sinf(angle);
            glVertex2f(xCoord, yCoord);
        }
        glEnd();
    }
}
void drawTiltedFilledSemiCircle(float centerX, float centerY, float radius, float tiltAngle, int isUpper){
    glPushMatrix();
    glTranslatef(centerX, centerY, 0.0f);
    glRotatef(tiltAngle, 0.0f, 0.0f, 1.0f);

    glBegin(GL_TRIANGLE_FAN); // Use GL_TRIANGLE_FAN for a filled shape
    glVertex2f(0.0f, 0.0f);   // Center of the semicircle

    if (isUpper)
    {
        for (int i = 0; i <= 180; i++)
        {                                        // Upper semicircle
            float theta = i * 3.14159f / 180.0f; // Convert degrees to radians
            float x = radius * cosf(theta);
            float y = radius * sinf(theta);
            glVertex2f(x, y);
        }
    }
    else
    {
        for (int i = 180; i <= 360; i++)
        {                                        // Lower semicircle
            float theta = i * 3.14159f / 180.0f; // Convert degrees to radians
            float x = radius * cosf(theta);
            float y = radius * sinf(theta);
            glVertex2f(x, y);
        }
    }

    glEnd();
    glPopMatrix();
}
void drawHollowCircle(float centerX, float centerY, float radius, int segments) {
    glBegin(GL_LINE_LOOP);
    for (int i = 0; i < segments; i++) {
        float theta = 2.0f * PI * i / segments; // Angle for each segment
        float x = radius * cosf(theta);        // X-coordinate
        float y = radius * sinf(theta);        // Y-coordinate
        glVertex2f(centerX + x, centerY + y); // Add vertex on the circle
    }
    glEnd();
}
void drawRightHollowSemiCircle(float centerX, float centerY, float radius, float angle, int segments) {
    glPushMatrix();
    glTranslatef(centerX, centerY, 0.0f);
    glRotatef(angle, 0.0f, 0.0f, 1.0f);

    glBegin(GL_LINE_STRIP);
    for (int i = 0; i <= segments; i++) {
        float theta = (-PI / 2.0f) + (PI * i / segments); // Angle from -PI/2 to +PI/2
        float x = radius * cosf(theta);
        float y = radius * sinf(theta);
        glVertex2f(x, y);
    }
    glEnd();

    glPopMatrix();
}
void drawTiltedUpperHollowEllipse(float centerX, float centerY, float radiusX, float radiusY, float angle, int segments) {
    glPushMatrix();
    glTranslatef(centerX, centerY, 0.0f);
    glRotatef(angle, 0.0f, 0.0f, 1.0f);
    glBegin(GL_LINE_STRIP);
    for (int i = 0; i <= segments / 2; i++) {
        float theta = PI * i / (segments / 2);
        float x = radiusX * cosf(theta);
        float y = radiusY * sinf(theta);
        glVertex2f(x, y);
    }
    glEnd();
    glPopMatrix();
}
void drawTiltedHollowEllipse(float centerX, float centerY, float radiusX, float radiusY, float angle, int segments) {
    glPushMatrix();
    glTranslatef(centerX, centerY, 0.0f);
    glRotatef(angle, 0.0f, 0.0f, 1.0f);
    glBegin(GL_LINE_LOOP);
    for (int i = 0; i < segments; i++) {
        float theta = 2.0f * PI * i / segments;
        float x = radiusX * cosf(theta);
        float y = radiusY * sinf(theta);
        glVertex2f(x, y);
    }
    glEnd();
    glPopMatrix();
}
void drawTiltedFilledEllipse(float centerX, float centerY, float radiusX, float radiusY, float angle, int segments) {
    glPushMatrix();
    glTranslatef(centerX, centerY, 0.0f);
    glRotatef(angle, 0.0f, 0.0f, 1.0f);
    glBegin(GL_POLYGON);
    for (int i = 0; i < segments; i++) {
        float theta = 2.0f * PI * i / segments;
        float x = radiusX * cosf(theta);
        float y = radiusY * sinf(theta);
        glVertex2f(x, y);
    }
    glEnd();
    glPopMatrix();
}
void drawFilledCircle(float x, float y, float radius, int segments) {
    glBegin(GL_POLYGON);
    for (int i = 0; i < segments; i++) {
        float angle = 2.0f * PI * i / segments;
        float dx = radius * cosf(angle);
        float dy = radius * sinf(angle);
        glVertex2f(x + dx, y + dy);
    }
    glEnd();
}
void drawFilledEllipse(float centerX, float centerY, float a, float b) {
    const int segments = 200;
    const float step = 2 * M_PI / segments;
    glBegin(GL_TRIANGLE_FAN);
    glVertex2f(centerX, centerY);
    for (float theta = 0.0f; theta <= 2 * M_PI; theta += step) {
        float x = centerX + a * cos(theta);
        float y = centerY + b * sin(theta);
        glVertex2f(x, y);
    }
    glVertex2f(centerX + a * cos(0.0f), centerY + b * sin(0.0f));
    glEnd();
}
void drawHollowEllipse(float centerX, float centerY, float a, float b, int segments) {
    const float step = 2 * M_PI / segments;
    glBegin(GL_LINE_LOOP);
    for (float theta = 0.0f; theta < 2 * M_PI; theta += step) {
        float x = centerX + a * cos(theta);
        float y = centerY + b * sin(theta);
        glVertex2f(x, y);
    }
    glEnd();
}
void drawLowerHollowTiltedEllipse(float centerX, float centerY, float radiusX, float radiusY, float angle, int segments) {
    glPushMatrix();
    glTranslatef(centerX, centerY, 0.0f); // Move to the center
    glRotatef(angle, 0.0f, 0.0f, 1.0f);  // Apply tilt

    glBegin(GL_LINE_STRIP);
    for (int i = 0; i <= segments; i++) {
        float theta = PI + (PI * i / segments); // Angles from PI to 2PI (lower half)
        float x = radiusX * cosf(theta);       // X-coordinate
        float y = radiusY * sinf(theta);       // Y-coordinate
        glVertex2f(x, y);
    }
    glEnd();

    glPopMatrix();

}
void drawTiltedHollowSemiCircle(float centerX, float centerY, float radius, float tiltAngle, int isUpper) {
    glPushMatrix();
    glTranslatef(centerX, centerY, 0.0f);
    glRotatef(tiltAngle, 0.0f, 0.0f, 1.0f);
    glBegin(GL_LINE_STRIP);

    if (isUpper) {
        for (int i = 0; i <= 180; i++) {
            float theta = i * 3.14159f / 180.0f; // Convert degrees to radians
            float x = radius * cosf(theta);
            float y = radius * sinf(theta);
            glVertex2f(x, y);
        }
    } else {

        for (int i = 180; i <= 360; i++) {
            float theta = i * 3.14159f / 180.0f; // Convert degrees to radians
            float x = radius * cosf(theta);
            float y = radius * sinf(theta);
            glVertex2f(x, y);
        }
    }

    glEnd();
    glPopMatrix();
}
float randomFloat(float min, float max) {
    return min + static_cast<float>(rand()) / (static_cast<float>(RAND_MAX / (max - min)));
}

//Animation variables

bool thunderVisible = false;
clock_t lastToggleTime = 0;
const int thunderDuration = 200; // 200ms flash duration
bool S4_beamActive = false; // Flag to track beam activation


bool isSpinning = true;
float rotationAngle = 0.0f;
float rotationSpeed = 1.0f; // Adjust the speed as needed


float RightBallonX = 0.0f;
float RightBallonSpeed = .03;
float LeftBallonX = 0.0f;
float LeftBallonSpeed = .03;



bool  JuiceSignVisible=true;
int   JuiceSignelapsedTime=0;
//S4_Stars vars
const int numS4_Stars = 100;
float starX[numS4_Stars];
float starY[numS4_Stars];
float starBrightness[numS4_Stars];
bool starVisible[numS4_Stars];

//Waves
float Wave1X= 0.0f;
float Wave1Speed=0.015f;
float Wave2X= 0.0f;
float Wave2Speed=0.020f;
float Wave3X= 0.0f;
float Wave3Speed=0.01f;

//LightHouseBeam
float S4_beamAngle =0.0f;
float S4_BeamSpeed = 1.0f;
float S4_BeamRad = 1.5f;
float S4_MinRad = 0.5f;
float S4_LightWidth = 0.2f;

//S4_sandcastle_flag
float waveOffset = 0.0;

bool  s2_isDay = true;
bool  s2_isMoving = true;

bool  s2_isActionTriggered = false;
float s2_actionTimer = 0.0f;

float s2_cloud1X = 2.42f, s2_cloud1Y = 13.85f;//l14
float s2_cloud2X = 5.71f, s2_cloud2Y = 13.05f;//l5
float s2_cloud3X = 9.0f, s2_cloud3Y = 14.0f;//l12
float s2_cloud4X = 13.05f, s2_cloud4Y = 12.89f;//l13
float s2_cloud5X = 16.08f, s2_cloud5Y = 14.15f;//l15
float s2_cloudSpeed = 0.03f; // Speed of the clouds


float s2_bird1X = 29.22f, s2_bird1Y = 12.53f;
float s2_bird2X = 23.488f, s2_bird2Y = 13.369f;
float s2_birdSpeed = 0.02f;
float s2_wingFlap = 0.0f; // Variable to control wing flapping


// Global variable to store the man's Initial position
float s2_mannX = 13.5f;
float s2_mannY = 10.7f;
float s2_handleAngleE = 0.0f;
bool  s2_handleMovingForward = true;  // To simulate handle's movement direction


float s2_handleAngle = 360.0f;  // Angle to simulate handle movement
float s2_waterY = 9.98f;  // Initial Y position of the water drop


//moving man with box
// Initialize position
float s2_manX = 19.2f;
float s2_manSpeed = 0.05f;
bool  s2_manMovingRight = true; // Initially moving to the right


// Kite parameters
//Initial
float s2_kiteX = 11.13f;
float s2_kiteY = 13.55f;
float s2_kiteAmplitude = 0.5f;  // Amplitude of the kite's up and down movement
float s2_kiteFrequency = 0.005f;  // Speed of the kite's movement


// Duck movement
float s2_duckX = 0.0f;
float s2_duckSpeed = 0.05f;
bool  s2_movingRight = true; // Direction flag


// Boat movement
float s2_boatX = 4.0f; // Starts from the opposite side of the pond
float s2_boatSpeed = 0.05f;
bool  s2_boatMovingLeft = false; // Boat direction flag




S4_SoilAndDetails(){
glBegin(GL_POLYGON);
        glColor3ub(240,214,138);
        glVertex2f(0.0,0.0);
        glVertex2f(30.0,0.0);
        glVertex2f(30.0,5.0);
        glVertex2f(10.0202451819864, 7.7104130643092);
        glVertex2f(0.0,6.0);
    glEnd();
    glLineWidth(2.3);
    glBegin(GL_LINE_LOOP);
        glColor3ub(0,0,0);
        glVertex2f(0.0,0.0);
        glVertex2f(30.0,0.0);
        glVertex2f(30.0,5.0);
        glVertex2f(10.0202451819864, 7.7104130643092);
        glVertex2f(0.0,6.0);
    glEnd();
    glColor3ub(240,214,138);
    drawTiltedFilledEllipse(4.6510528439121, 6.778886816893,5.3,0.13,10.0,200);
    glColor3ub(0,0,0);
    drawTiltedUpperHollowEllipse(4.6510528439121, 6.778886816893,5.3,0.13,10.0,200);

    //StarFish1
    glBegin(GL_POLYGON);
        glColor3ub(237,102,66);
        glVertex2f(23.0883842426613, 0.7115871386537);
        glVertex2f(23.210950797015, 0.6616526165096);
        glVertex2f(23.2565020168367, 0.5546583040691);
        glVertex2f(23.3219439043131, 0.6425958403655);
        glVertex2f(23.4589628562167, 0.6385057223982);
        glVertex2f(23.3771604968713, 0.7346234946291);
        glVertex2f(23.4303320304458, 0.8409665617782);
        glVertex2f(23.3158087273622, 0.8164258539746);
        glVertex2f(23.2115107191967, 0.8859578594182);
        glVertex2f(23.2064112950019, 0.770600664824);
        glVertex2f(23.0883842426613, 0.7115871386537);
    glEnd();
    glLineWidth(2.0);
    glBegin(GL_LINE_LOOP);
        glColor3ub(0,0,0);
        glVertex2f(23.0883842426613, 0.7115871386537);
        glVertex2f(23.210950797015, 0.6616526165096);
        glVertex2f(23.2565020168367, 0.5546583040691);
        glVertex2f(23.3219439043131, 0.6425958403655);
        glVertex2f(23.4589628562167, 0.6385057223982);
        glVertex2f(23.3771604968713, 0.7346234946291);
        glVertex2f(23.4303320304458, 0.8409665617782);
        glVertex2f(23.3158087273622, 0.8164258539746);
        glVertex2f(23.2115107191967, 0.8859578594182);
        glVertex2f(23.2064112950019, 0.770600664824);
        glVertex2f(23.0883842426613, 0.7115871386537);
    glEnd();
    //StarFish2
    glBegin(GL_POLYGON);
        glColor3ub(237,102,66);
        glVertex2f(21.9342003016334, 0.9276408943363);
        glVertex2f(22.1219387045743, 0.8337716928659);
        glVertex2f(22.1722257767906, 0.6091561036331);
        glVertex2f(22.3130295789962, 0.8069519210172);
        glVertex2f(22.5510550541533, 0.7667222632442);
        glVertex2f(22.3934888945423, 0.9712230235904);
        glVertex2f(22.5074729248992, 1.1220842402393);
        glVertex2f(22.2795048641853, 1.0986169398717);
        glVertex2f(22.1252911760553, 1.2293633276341);
        glVertex2f(22.1219387045743, 1.0349199817311);
    glEnd();
    glBegin(GL_LINE_LOOP);
        glColor3ub(0,0,0);
        glVertex2f(21.9342003016334, 0.9276408943363);
        glVertex2f(22.1219387045743, 0.8337716928659);
        glVertex2f(22.1722257767906, 0.6091561036331);
        glVertex2f(22.3130295789962, 0.8069519210172);
        glVertex2f(22.5510550541533, 0.7667222632442);
        glVertex2f(22.3934888945423, 0.9712230235904);
        glVertex2f(22.5074729248992, 1.1220842402393);
        glVertex2f(22.2795048641853, 1.0986169398717);
        glVertex2f(22.1252911760553, 1.2293633276341);
        glVertex2f(22.1219387045743, 1.0349199817311);
    glEnd();
    glBegin(GL_LINES);
        glVertex2f(22.1839819405247, 0.9382506113054);
        glVertex2f(21.9342003016334, 0.9276408943363);
    glEnd();
    glBegin(GL_LINES);
        glVertex2f(22.1722257767906, 0.6091561036331);
        glVertex2f(22.2219532408381, 0.8770746274671);
    glEnd();
    glBegin(GL_LINES);
        glVertex2f(22.5510550541533, 0.7667222632442);
        glVertex2f(22.3021148748332, 0.925593511201);
    glEnd();
    glBegin(GL_LINES);
        glVertex2f(22.1252911760553, 1.2293633276341);
        glVertex2f(22.2333499034054, 0.9999204267369);
    glEnd();
    glBegin(GL_LINES);
        glVertex2f(22.5074729248992, 1.1220842402393);
        glVertex2f(22.3126624582536, 1.0078646618801);
    glEnd();






}
Crab(){
glLineWidth(3.5);
glColor3ub(223,90,0);
drawTiltedUpperHollowEllipse(19.9968555059047, 0.6650250729416,0.26,0.16,25,100);
drawTiltedUpperHollowEllipse(20.0783797093594, 0.4408529594857,0.26,0.16,45,100);
drawTiltedUpperHollowEllipse(20.1960007266331, 0.2867346314848,0.26,0.16,65,100);
drawTiltedUpperHollowEllipse(20.9235977451834, 0.3778045525762,0.26,0.16,310,100);
drawTiltedUpperHollowEllipse(20.9247038387975, 0.6048481506457,0.26,0.16,320,100);
drawTiltedUpperHollowEllipse(20.9262105958631, 0.8276252841639,0.26,0.16,350,100);
    //body
    glColor3ub(255,155,72);
drawFilledCircle(20.5006070294031, 0.7757774288858,0.30,100);
    glLineWidth(2.0);
    glColor3ub(0,0,0);
drawHollowCircle(20.5006070294031, 0.7757774288858,0.30,100);
glColor3ub(223,90,0);
drawFilledEllipse(20.4938970981978, 0.6739420382593,0.23,0.20);
glColor3ub(0,0,0);
glLineWidth(1.6);
drawHollowEllipse(20.4938970981978, 0.6739420382593,0.23,0.20,100);
    //eyes
        glBegin(GL_LINES);
    glVertex2f(20.33,1.05);
    glVertex2f(20.3157109973147, 1.2786094447782);
        glEnd();
        glBegin(GL_LINES);
    glVertex2f(20.4778652085183, 1.0658130895681);
    glVertex2f(20.4973129529125, 1.2933710347488);
        glEnd();
        glColor3ub(223,90,0);
        drawFilledCircle(20.3, 1.3,0.089,100);
        drawFilledCircle(20.4838291607995, 1.3202622878155,0.089,100);
        glColor3ub(0,0,0);
        drawHollowCircle(20.3, 1.3,0.089,100);
        drawHollowCircle(20.4838291607995, 1.3202622878155,0.089,100);
    // Hand
    glLineWidth(3.5);
    glColor3ub(255,152,23);
    drawTiltedHollowSemiCircle(20.1026011323627, 1.1087296254482,0.18,130,true);
    drawTiltedHollowSemiCircle(20.760147969832, 1.2270330313528,0.18,70,false);
    drawTiltedSemicircle(20.8536901977565, 1.5186646831174,0.18,82,true);

}
SoilStrip(){
 //Strip
    glBegin(GL_POLYGON);
        glColor3ub(240,214,138);
        glVertex2f(12.7607151034867, 2.4001033496419);
        glVertex2f(22.0099054874694, 2.3264621314034);
        glVertex2f(22.0169767339404, 2.7370957014211);
        glVertex2f(12.9587100046729, 2.7158819620083);
        glVertex2f(0.0,6.0);
    glEnd();
    glColor3ub(0,0,0);
    glLineWidth(2.3);
    glBegin(GL_LINES);
    glVertex2f(12.9587100046729, 2.7158819620083);
        glVertex2f(13.3,2.71);
        glEnd();
        drawTiltedHollowSemiCircle(13.4610549178167, 2.7297831661403,0.18,0.0,true);
        glBegin(GL_LINES);
    glVertex2f(13.6537605775349, 2.734697529998);
        glVertex2f(15.6446411561796, 2.7409779103723);
        glEnd();
        glBegin(GL_LINES);
    glVertex2f(14.8048058090687, 2.5557550454622);
        glVertex2f(15.4198850956766, 2.5557550454622);
        glEnd();
        drawTiltedHollowSemiCircle(15.5751194351604, 2.5377106522981,0.18,170.0,false);
        glBegin(GL_LINES);
    glVertex2f(15.7005752380506, 2.5251709925369);
        glVertex2f(15.9542742399106, 2.5251709925369);
        glEnd();
        drawTiltedHollowSemiCircle(16.1001875433592, 2.5289278494811,0.18,0.0,true);
        glBegin(GL_LINES);
    glVertex2f(17.3926731328713, 2.4214562426168);
        glVertex2f(17.6188444410086, 2.4261195685577);
        glEnd();
        drawHollowEllipse(17.735427589533, 2.4774161539085,0.16,0.05,200);
        glBegin(GL_LINES);
    glVertex2f(17.7657392081493, 2.3724913202365);
        glVertex2f(18.2507251060107, 2.3771546461775);
        glEnd();

        glBegin(GL_LINES);
    glVertex2f(18.5996720186478, 2.4149529291167);
        glVertex2f(19.1610561032512, 2.4197924470874);
        glEnd();
        drawHollowEllipse(19.4159054996686, 2.4132785469701,0.31,0.05,200);
        drawTiltedHollowSemiCircle(19.8756893899823, 2.427706648643,0.18,2.0,true);
        glBegin(GL_LINES);
    glVertex2f(19.99995849591, 2.4143110009995);
        glVertex2f(21.125900399363, 2.410113411146);
        glEnd();

}
//S4_SAC
S4_SkyAndClouds(){
glBegin(GL_POLYGON);
        glColor3ub(0,7,60);
        glVertex2f(0.0,10.0);
        glVertex2f(15.2798174002007, 11.7817611608872);
        glVertex2f(30.0,10.0);
        glVertex2f(30.0,15.0);
        glVertex2f(0.0,15.0);
    glEnd();
    glBegin(GL_TRIANGLES);
    glColor3ub(40, 36, 66);
    glVertex2f(0.0,10.0);
        glVertex2f(30.0,10.0);
        glVertex2f(15.2798174002007, 11.7817611608872);
        glEnd();
    //Border clouds
    glColor3ub(0,7,60);
drawFilledCircle(28.0532136084675, 10.1660269945496,3.1,100);
drawFilledCircle(24.3511674505249, 10.7549888833132,1.46,100);
drawFilledCircle(21.9953198954705, 9.7948574835388,1.42,100);
drawFilledCircle(19.38,11.28,1.9,100);
drawFilledCircle(16.19,11.67,1.8,100);
drawFilledCircle(13.9839692884568, 11.6864296905942,0.67,100);
drawFilledCircle(12.52,11.46,0.9,100);
drawFilledCircle(10.38,11.21,1.4,100);
drawFilledCircle(7.17,10.83,1.9,100);
drawFilledCircle(5.2249926647529, 9.530373906298,1.13,100);
drawFilledCircle(4.0,10.46,0.9,100);
drawFilledCircle(1.5376472609278, 10.228641972803,1.7,100);
}
//S4_Sts
void S4_Stars() {
    glPointSize(4.0);
    glBegin(GL_POINTS);
    for (int i = 0; i < numS4_Stars; i++) {
        if (starBrightness[i] == 0) {
            starBrightness[i] = randomFloat(0.5, 1.0);
            starVisible[i] = true;
        }

        if (starVisible[i]) {
            glColor3f(starBrightness[i], starBrightness[i], starBrightness[i]);
            float x = randomFloat(0.0,30.0);
            float y = randomFloat(10.0,30.0);
            glVertex2f(x, y);
        }
    }
    glEnd();
}
Cloud1(){
    glColor3ub(255,255,255);
    glLineWidth(3.0);
    drawTiltedHollowSemiCircle(3.2882887823755, 13.2989546744021,0.96,0,true);
    drawTiltedHollowSemiCircle(4.4254240391536, 13.6101110879483,1.24,0,true);
    glColor3ub(11,11,84);
    drawFilledCircle(3.2882887823755, 13.2989546744021,0.96,100);
    drawFilledCircle(4.4254240391536, 13.6101110879483,1.24,100);
   /* glColor3ub(255,255,255);
    glLineWidth(1.5);
    drawTiltedHollowSemiCircle(3.2882887823755, 13.2989546744021,0.96,0,true);
    drawTiltedHollowSemiCircle(4.4254240391536, 13.6101110879483,1.24,0,true);*/
    glBegin(GL_POLYGON);
        glColor3ub(3,37,108);
        //glColor3ub(17,16,122);
       // glColor3ub(0,0,0);
        glVertex2f(3.0,12.0);
        glVertex2f(6.0,12.0);
        glVertex2f(6.0075306509456, 13.1900535852944);
        glVertex2f(2.9918000596263, 13.1814924598916);
    glEnd();


    drawFilledCircle(5.760442341656, 12.9569400811648,0.96,100);
    drawFilledCircle(4.2610292824076, 13.6609790551645,0.96,100);
    drawFilledCircle(3.2597484511024, 12.9417491622551,0.96,100);
    glColor3ub(255,255,255);
    glLineWidth(1.5);
    glBegin(GL_LINES);
    glVertex2f(3.0,12.0);
        glVertex2f(6.0,12.0);
        glEnd();



}
//S4_SW
S4_SeaWater(){
glBegin(GL_POLYGON);
        glColor3ub(14,49,119);
       // glColor3ub(0,0,0);
        glVertex2f(22.6855606826301, 2.0214219028557);
        glVertex2f(30.0, 0.0);
        glVertex2f(30.0,7.26);
        glVertex2f(23.5,5.87);
    glEnd();
drawTiltedFilledEllipse(23.9939891124046, 2.3289818018727,1.86,0.78,150,200);
drawTiltedFilledEllipse(23.8562703425279, 3.6248601739538,1.86,0.78,160,200);
drawTiltedFilledEllipse(24.453942398449637, 5.226398521455826,1.86,0.78,30,200);
drawTiltedFilledEllipse(26.72,6.45,3.20,0.58,10,200);
drawTiltedFilledEllipse(28.217,7.3,2.50,0.58,15,200);


}
Wave1(){
    glPushMatrix();
    glTranslatef(Wave1X,0.0,0.0);
glColor3ub(14,49,119);
drawTiltedFilledEllipse(23.5836825899908, 3.0533863656836,1.79,0.58,0,200);
glPopMatrix();
}
Wave2(){
    glPushMatrix();
    glTranslatef(Wave2X,0.0,0.0);
glColor3ub(14,49,119);
drawTiltedFilledEllipse(23.6655352338137, 3.4970129185738,1.79,0.78,0,200);
glPopMatrix();
}
Wave3(){
    glPushMatrix();
    glTranslatef(Wave3X,0.0,0.0);
glColor3ub(14,49,119);
drawTiltedFilledEllipse(23.6655352338137, 4.1170129185738,1.79,0.78,170,200);
glPopMatrix();
}
//S4_LH
S4_LightHouseAndCliff(){
    //Cliff
    glLineWidth(2.0);
    glBegin(GL_POLYGON);
        glColor3ub(49,19,19);
       // glColor3ub(0,0,0);
        glVertex2f(26, 7);
        glVertex2f(29.8,8.18);
        glVertex2f(29.3,8.42);
        glVertex2f(25.304049404987, 8.4129467300064);
        glVertex2f(25.2923647583373, 8.1208305637635);
        glVertex2f(25.304049404987, 8.4129467300064);
    glEnd();
    glBegin(GL_LINE_LOOP);
        glColor3ub(0,0,0);
       // glColor3ub(0,0,0);
        glVertex2f(26, 7);
        glVertex2f(29.8,8.18);
        glVertex2f(29.3,8.42);
        glVertex2f(25.304049404987, 8.4129467300064);
        glVertex2f(25.2923647583373, 8.1208305637635);
        glVertex2f(25.304049404987, 8.4129467300064);
    glEnd();
    glBegin(GL_POLYGON);
        glColor3ub(49,19,19);
       // glColor3ub(0,0,0);
        glVertex2f(20.8288297381452, 6.3097103330574);
        glVertex2f(22.5231035023542, 5.725478000571);
        glVertex2f(24.0537922134672, 6.2746563931082);
        glVertex2f(26.0,7.0);
        glVertex2f(25.304049404987, 8.4129467300064);
        glVertex2f(25.487590884518, 6.5174144923139);
        glVertex2f(25.2923647583373, 8.1208305637635);
        glVertex2f(23.7850453405237, 8.0857766238143);
        glVertex2f(22.7334271420491, 7.7001832843737);
        glVertex2f(22.4880495624051, 7.3029052982833);
        glVertex2f(22.1491948095633, 7.3029052982833);
        glVertex2f(21.3896927773316, 6.9640505454415);
    glEnd();
    glBegin(GL_LINE_LOOP);
        glColor3ub(0,0,0);
       // glColor3ub(0,0,0);
        glVertex2f(20.8288297381452, 6.3097103330574);
        glVertex2f(22.5231035023542, 5.725478000571);
        glVertex2f(24.0537922134672, 6.2746563931082);
        glVertex2f(26.0,7.0);
        glVertex2f(25.304049404987, 8.4129467300064);
        glVertex2f(25.487590884518, 6.5174144923139);
        glVertex2f(25.2923647583373, 8.1208305637635);
        glVertex2f(23.7850453405237, 8.0857766238143);
        glVertex2f(22.7334271420491, 7.7001832843737);
        glVertex2f(22.4880495624051, 7.3029052982833);
        glVertex2f(22.1491948095633, 7.3029052982833);
        glVertex2f(21.3896927773316, 6.9640505454415);
    glEnd();
    glBegin(GL_POLYGON);
        glColor3ub(0,46,98);
       // glColor3ub(0,0,0);
        glVertex2f(25.74,8.41);
        glVertex2f(28.0297837002298, 8.433060276055);
        glVertex2f(27.8517786449097, 8.6245621634275);
        glVertex2f(25.7451125356696, 8.6406530828077);
    glEnd();
    glBegin(GL_LINE_LOOP);
        glColor3ub(0,0,0);
       // glColor3ub(0,0,0);
        glVertex2f(25.74,8.41);
        glVertex2f(28.0297837002298, 8.433060276055);
        glVertex2f(27.8517786449097, 8.6245621634275);
        glVertex2f(25.7451125356696, 8.6406530828077);
    glEnd();
    //hut
    glBegin(GL_POLYGON);
        glColor3ub(170,170,170);
       // glColor3ub(0,0,0);
        glVertex2f(26.104049404987, 8.6529467300064);
        glVertex2f(26.614686547565, 8.6587188399028);
        glVertex2f(26.6007412021609, 9.3189353857522);
        glVertex2f(26.161743159091, 9.310962945632);
    glEnd();
    glBegin(GL_LINE_LOOP);
        glColor3ub(0,0,0);
       // glColor3ub(0,0,0);
        glVertex2f(26.104049404987, 8.6529467300064);
        glVertex2f(26.614686547565, 8.6587188399028);
        glVertex2f(26.6007412021609, 9.3189353857522);
        glVertex2f(26.161743159091, 9.310962945632);
    glEnd();
    glBegin(GL_POLYGON);
        glColor3ub(170,170,170);
       // glColor3ub(0,0,0);
        glVertex2f(26.614686547565, 8.6587188399028);
        glVertex2f(27.5931092115914, 8.650457968969);
        glVertex2f(27.5414550081318, 9.1916958402438);
        glVertex2f(26.6007412021609, 9.3189353857522);
    glEnd();
    glBegin(GL_LINE_LOOP);
        glColor3ub(0,0,0);
       // glColor3ub(0,0,0);
        glVertex2f(26.614686547565, 8.6587188399028);
        glVertex2f(27.5931092115914, 8.650457968969);
        glVertex2f(27.5414550081318, 9.1916958402438);
        glVertex2f(26.6007412021609, 9.3189353857522);
    glEnd();
    glBegin(GL_POLYGON);
        glColor3ub(181,43,36);
       // glColor3ub(0,0,0);
        glVertex2f(26.1861743159091, 9.310962945632);
        glVertex2f(26.6007412021609, 9.3189353857522);
        glVertex2f(27.5414550081318, 9.1916958402438);
        glVertex2f(26.6867455656752, 9.5850019220171);
    glEnd();
    glBegin(GL_POLYGON);
        glColor3ub(170,170,170);
       // glColor3ub(0,0,0);
        glVertex2f(26.6867455656752, 9.5850019220171);
        glVertex2f(26.9963728065496, 9.5247571828284);
        glVertex2f(27, 9.8);
        glVertex2f(26.6962044060644, 9.8068504450817);
    glEnd();

    glBegin(GL_LINE_LOOP);
        glColor3ub(0,0,0);
       // glColor3ub(0,0,0);
        glVertex2f(26.1861743159091, 9.310962945632);
        glVertex2f(26.6007412021609, 9.2789353857522);
        glVertex2f(27.5414550081318, 9.1916958402438);
        glVertex2f(26.6867455656752, 9.5850019220171);
    glEnd();
    glColor3ub(181,43,36);
    drawTiltedFilledEllipse(27.08,9.40,0.45,0.11,160,100);
    //Lighthouse
    glBegin(GL_POLYGON);
        glColor3ub(187,187,187);
       // glColor3ub(0,0,0);
        glVertex2f(25.2923647583373, 8.1208305637635);
        glVertex2f(25.9,8.4);
        glVertex2f(25.8407880860809, 11.9029677015332);
        glVertex2f(25.4532233596917, 11.9029677015332);
    glEnd();
    glBegin(GL_LINE_LOOP);
        glColor3ub(0,0,0);
       // glColor3ub(0,0,0);
        glVertex2f(25.2923647583373, 8.1208305637635);
        glVertex2f(25.9,8.4);
        glVertex2f(25.8407880860809, 11.9029677015332);
        glVertex2f(25.4532233596917, 11.9029677015332);
    glEnd();
    glBegin(GL_POLYGON);
        glColor3ub(72,89,119);
       // glColor3ub(0,0,0);
        glVertex2f(25.9,8.6);
        glVertex2f(26.412696543204, 8.6394521237955);
        glVertex2f(26.2,11.9);
        glVertex2f(25.8407880860809, 11.9029677015332);
    glEnd();
    glBegin(GL_LINE_LOOP);
        glColor3ub(0,0,0);
       // glColor3ub(0,0,0);
        glVertex2f(25.9,8.6);
        glVertex2f(26.412696543204, 8.6394521237955);
        glVertex2f(26.2,11.9);
        glVertex2f(25.8407880860809, 11.9029677015332);
    glEnd();
    glBegin(GL_POLYGON);
        glColor3ub(72,89,119);
       // glColor3ub(0,0,0);
        glVertex2f(25.2802498056891, 11.9518129854215);
        glVertex2f(25.4532233596917, 11.9029677015332);
        glVertex2f(26.2,11.9);
        glVertex2f(26.1668738849216, 12.2497768153275);
        glVertex2f(25.4532233596917, 12.3);
        glVertex2f(25.2729823952036, 12.2788464572695);
    glEnd();
    glBegin(GL_LINE_LOOP);
        glColor3ub(0,0,0);
       // glColor3ub(0,0,0);
        glVertex2f(25.2802498056891, 11.9518129854215);
        glVertex2f(25.4532233596917, 11.9029677015332);
        glVertex2f(26.2,11.9);
        glVertex2f(26.1668738849216, 12.2497768153275);
        glVertex2f(25.4532233596917, 12.3);
        glVertex2f(25.2729823952036, 12.2788464572695);
    glEnd();
    glBegin(GL_POLYGON);
        glColor3ub(170,170,170);
       // glColor3ub(0,0,0);
        glVertex2f(25.4,12.3);
        glVertex2f(26.0110294710448, 12.2781233508639);
        glVertex2f(26.0181666462815, 12.7877256174302);
        glVertex2f(25.4, 12.7848627926669);
    glEnd();
    glBegin(GL_LINE_LOOP);
        glColor3ub(0,0,0);
       // glColor3ub(0,0,0);
        glVertex2f(25.4,12.3);
        glVertex2f(26.0110294710448, 12.2781233508639);
        glVertex2f(26.0181666462815, 12.7877256174302);
        glVertex2f(25.4, 12.7848627926669);
    glEnd();

}
//S4_LHB
S4_LightHouseBeam(){
    glPushMatrix();
glTranslatef(25.7722500920385f, 12.6338337768541f, 0.0f);
glRotatef(S4_beamAngle, 0.0f, 0.0f, 1.0f);
float dynamicRadius = S4_BeamSpeed - (fabs(fmod(S4_beamAngle, 360.0f) - 180.0f) / 180.0f) * (S4_BeamRad - S4_MinRad);
glColor3f(1.0f, 1.0f, 0.0f);
glBegin(GL_TRIANGLES);
    glVertex2f(0.0f, 0.0f);
    glVertex2f(-S4_LightWidth, -dynamicRadius* 5.0f);
    glVertex2f(S4_LightWidth, -dynamicRadius* 5.0f);
glEnd();
glPopMatrix();

}
//S4_BBQ_p
S4_BBQ_place(){
glLineWidth(3.5f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(10.7062381967077, 1.6817318006575);
    glVertex2f(10.3570527256264, 0.3847571937844);
    glEnd();
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(11.0077489829913, 1.7216014373304);
    glVertex2f(11, 0.4);
    glEnd();
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(11.3048418614184, 1.669260890976);
    glVertex2f(11.6437478710066, 0.3837580736566);
    glEnd();
drawHollowEllipse(11.0,1.1,0.45,0.08,100);
glColor3ub(190, 49, 68);
drawLowerSemicircle(11, 2.4,0.82,100);
glColor3ub(0,0,0);
glLineWidth(2.0f);
drawTiltedHollowSemiCircle(11, 2.4,0.82,0,false);
glColor3ub(241, 74, 0);
drawFilledEllipse(11, 2.4,0.82,0.13);
glColor3ub(0,0, 0);
drawHollowEllipse(11, 2.4,0.82,0.13,100);
glLineWidth(3.5f);
glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(11.6,2.3);
    glVertex2f(11.7,2.45);
    glEnd();
glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(11.5,2.5);
    glVertex2f(11.35,2.3);
    glEnd();
glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(11.15,2.3);
    glVertex2f(11.30,2.5);
    glEnd();
glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(10.82,2.29);
    glVertex2f(11.00,2.53);
    glEnd();
glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(10.63,2.30);
    glVertex2f(10.79,2.53);
    glEnd();
glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(10.45,2.32);
    glVertex2f(10.58,2.52);
    glEnd();
glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(10.27,2.3);
    glVertex2f(10.4,2.5);
    glEnd();


}
//S4_BS
S4_BusStand(){
    glBegin(GL_POLYGON);
        glColor3ub(170,170,170);
       // glColor3ub(0,0,0);
        glVertex2f(1.0,0.0);
        glVertex2f(1.4, 0.0);
        glVertex2f(1.4,6.0);
        glVertex2f(1.0, 6.0);
    glEnd();
glLineWidth(2.0f);
    glBegin(GL_LINE_LOOP);
        glColor3ub(0,0,0);
        glVertex2f(1.0,0.0);
        glVertex2f(1.4, 0.0);
        glVertex2f(1.4,6.0);
        glVertex2f(1.0, 6.0);
    glEnd();
    glBegin(GL_QUADS);
        glColor3ub(170,170,170);
        glVertex2f(7.0,0.0);
        glVertex2f(7.4, 0.0);
        glVertex2f(7.4,6.0);
        glVertex2f(7.0, 6.0);
    glEnd();
glLineWidth(2.0f);
    glBegin(GL_LINE_LOOP);
        glColor3ub(0,0,0);
        glVertex2f(7.0,0.0);
        glVertex2f(7.4, 0.0);
        glVertex2f(7.4,6.0);
        glVertex2f(7.0, 6.0);
    glEnd();
    //roof
glBegin(GL_QUADS);
        glColor3ub(70,130,180);
        glVertex2f(0.6,6.0);
        glVertex2f(7.9, 6.0);
        glVertex2f(7.0,7.0);
        glVertex2f(1.6, 7.0);
    glEnd();
glColor3ub(70,130,180);
drawTiltedFilledEllipse(1.10,6.49,0.67,0.17,48,100);
drawTiltedFilledEllipse(7.44,6.49,0.67,0.17,360-48,100);
    //Bus Stop Board
glBegin(GL_QUADS);
        glColor3ub(166,102,65);
        glVertex2f(1.7,7.0);
        glVertex2f(6.8, 7.0);
        glVertex2f(6.8,8.0);
        glVertex2f(1.7, 8.0);
    glEnd();
glLineWidth(2.0f);
    glBegin(GL_LINE_LOOP);
        glColor3ub(0,0,0);
        glVertex2f(1.7,7.0);
        glVertex2f(6.8, 7.0);
        glVertex2f(6.8,8.0);
        glVertex2f(1.7, 8.0);
    glEnd();
    //BUS STOP logo
    //B
    glLineWidth(3.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(2.44,7.18);
    glVertex2f(2.44, 7.79);
    glEnd();
    glLineWidth(3.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(2.44, 7.79);
    glVertex2f(2.65, 7.79);
    glEnd();
    glLineWidth(3.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(2.44, 7.5);
    glVertex2f(2.70, 7.5);
    glEnd();
    glLineWidth(3.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(2.44, 7.18);
    glVertex2f(2.63, 7.18);
    glEnd();
glColor3ub(0,0,0);
drawRightHollowSemiCircle(2.64,7.66,0.14,0,100);
drawRightHollowSemiCircle(2.6,7.34,0.17,0,100);
    //U
    glLineWidth(3.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(2.9, 7.8);
    glVertex2f(2.9, 7.3);
    glEnd();
    glLineWidth(3.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(3.2, 7.8);
    glVertex2f(3.2, 7.3);
    glEnd();
drawRightHollowSemiCircle(3.05,7.32,0.16,270,100);
    //S
    glLineWidth(3.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(3.4, 7.7);
    glVertex2f(3.4, 7.4);
    glEnd();
    glLineWidth(3.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(3.6, 7.5);
    glVertex2f(3.6, 7.3);
    glEnd();
drawRightHollowSemiCircle(3.5,7.4,0.11,90,100);
drawRightHollowSemiCircle(3.5,7.64,0.11,90,100);
drawRightHollowSemiCircle(3.5,7.3,0.11,270,100);
    //S
    glLineWidth(3.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(4.15, 7.7);
    glVertex2f(4.15, 7.5);
    glEnd();
    glLineWidth(3.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(4.5, 7.45);
    glVertex2f(4.5, 7.3);
    glEnd();
drawRightHollowSemiCircle(4.32,7.64,0.17,90,200);
drawRightHollowSemiCircle(4.32,7.45,0.17,90,100);
drawRightHollowSemiCircle(4.33,7.32,0.16,270,100);
    //T
    glLineWidth(3.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(4.75, 7.2);
    glVertex2f(4.75, 7.82);
    glEnd();
    glLineWidth(3.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(4.54, 7.82);
    glVertex2f(4.94, 7.82);
    glEnd();
    //O
    glLineWidth(3.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(5.11, 7.4);
    glVertex2f(5.11, 7.6);
    glEnd();
    glLineWidth(3.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(5.51, 7.4);
    glVertex2f(5.51, 7.6);
    glEnd();
drawRightHollowSemiCircle(5.3,7.6,0.19,90,100);
drawRightHollowSemiCircle(5.3,7.4,0.19,270,100);
    //P
    glLineWidth(3.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(5.76, 7.2);
    glVertex2f(5.76, 7.8);
    glEnd();
    glLineWidth(3.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(5.95, 7.8);
    glVertex2f(5.76, 7.8);
    glEnd();
    glLineWidth(3.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(5.76, 7.5);
    glVertex2f(5.95, 7.5);
    glEnd();
drawRightHollowSemiCircle(5.95,7.64,0.15,0,100);
    //Body windows
    glLineWidth(7.0f);
    glBegin(GL_LINES);
    glColor3ub(170,170,170);
    glVertex2f(1.4, 5.0);
    glVertex2f(7.0, 5.0);
    glEnd();
    glLineWidth(7.0f);
    glBegin(GL_LINES);
    glColor3ub(170,170,170);
    glVertex2f(1.4, 1.0);
    glVertex2f(7.0, 1.0);
    glEnd();
    glBegin(GL_QUADS);
        glColor3ub(208,223,242);
        glVertex2f(2.0,1.19);
        glVertex2f(3.90, 1.19);
        glVertex2f(3.90,4.77);
        glVertex2f(2.0,4.77);
    glEnd();
    glBegin(GL_QUADS);
        glColor3ub(208,223,242);
        glVertex2f(4.4,1.19);
        glVertex2f(6.4, 1.19);
        glVertex2f(6.4,4.77);
        glVertex2f(4.4,4.77);
    glEnd();
    glLineWidth(2.0f);
    glBegin(GL_LINE_LOOP);
        glColor3ub(0,0,0);
        glVertex2f(2.0,1.19);
        glVertex2f(3.90, 1.19);
        glVertex2f(3.90,4.77);
        glVertex2f(2.0,4.77);
    glEnd();
    glLineWidth(2.0f);
    glBegin(GL_LINE_LOOP);
        glColor3ub(0,0,0);
        glVertex2f(4.4,1.19);
        glVertex2f(6.4, 1.19);
        glVertex2f(6.4,4.77);
        glVertex2f(4.4,4.77);
    glEnd();
    //White shade Win-1
    glBegin(GL_QUADS);
    glColor3ub(255,255,255);
    glVertex2f(2.0,3.88);
    glVertex2f(3.90, 1.9);
    glVertex2f(3.9,2.5);
    glVertex2f(2.0, 4.4);
    glEnd();
    //White shade Win-2
    glBegin(GL_QUADS);
    glColor3ub(255,255,255);
    glVertex2f(4.9,4.77);
    glVertex2f(6.4,3.4);
    glVertex2f(6.4,3.9);
    glVertex2f(5.4, 4.77);
    glEnd();
}
//S4_JS
void S4_BeachJuiceShop(){
glBegin(GL_QUADS);
    glColor3ub(145,67,81);
    glVertex2f(16.0,2.6);
    glVertex2f(16.3,2.6);
    glVertex2f(16.3,4.2);
    glVertex2f(16.0,4.2);
    glEnd();
glLineWidth(2.0f);
glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(16.0,2.6);
    glVertex2f(16.3,2.6);
    glVertex2f(16.3,4.2);
    glVertex2f(16.0,4.2);
    glEnd();
glLineWidth(2.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(16.15, 4.2);
    glVertex2f(16.15, 2.6);
    glEnd();
glBegin(GL_QUADS);
    glColor3ub(145,67,81);
    glVertex2f(15.4,4.2);
    glVertex2f(18.4,4.2);
    glVertex2f(18.4,4.4);
    glVertex2f(15.4,4.4);
    glEnd();
glLineWidth(2.0f);
glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(15.4,4.2);
    glVertex2f(18.4,4.2);
    glVertex2f(18.4,4.4);
    glVertex2f(15.4,4.4);
    glEnd();
glBegin(GL_QUADS);
    glColor3ub(94,44,71);
    glVertex2f(18.4,4.2);
    glVertex2f(20.0,4.2);
    glVertex2f(20.0,4.4);
    glVertex2f(18.4,4.4);
    glEnd();
glLineWidth(2.0f);
glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(18.4,4.2);
    glVertex2f(20.0,4.2);
    glVertex2f(20.0,4.4);
    glVertex2f(18.4,4.4);
    glEnd();

glBegin(GL_QUADS);
    glColor3ub(94,44,71);
    glVertex2f(18.7,2.6);
    glVertex2f(19.5,2.6);
    glVertex2f(19.5,4.2);
    glVertex2f(18.7,4.2);
    glEnd();
glLineWidth(2.5f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(18.7,2.6);
    glVertex2f(18.7,4.2);
    glEnd();
glBegin(GL_QUADS);
    glColor3ub(45,21,45);
    glVertex2f(19.5,2.6);
    glVertex2f(19.6,2.6);
    glVertex2f(19.6,4.2);
    glVertex2f(19.5,4.2);
    glEnd();
glLineWidth(2.0f);
glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(19.5,2.6);
    glVertex2f(19.6,2.6);
    glVertex2f(19.6,4.2);
    glVertex2f(19.5,4.2);
    glEnd();
glBegin(GL_QUADS);
    glColor3ub(145,67,81);
    glVertex2f(16.0,4.4);
    glVertex2f(16.3,4.4);
    glVertex2f(16.3,5.5);
    glVertex2f(16.0,5.5);
    glEnd();
glLineWidth(2.0f);
glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(16.0,4.4);
    glVertex2f(16.3,4.4);
    glVertex2f(16.3,5.5);
    glVertex2f(16.0,5.5);
    glEnd();
glLineWidth(2.5f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(16.2,4.2);
    glVertex2f(16.2,5.5);
    glEnd();
glBegin(GL_QUADS);
    glColor3ub(145,67,81);
    glVertex2f(17.0,4.4);
    glVertex2f(17.2,4.4);
    glVertex2f(17.2,5.5);
    glVertex2f(17.0,5.5);
    glEnd();
glLineWidth(2.0f);
glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(17.0,4.4);
    glVertex2f(17.2,4.4);
    glVertex2f(17.2,5.5);
    glVertex2f(17.0,5.5);
    glEnd();
glBegin(GL_QUADS);
    glColor3ub(145,67,81);
    glVertex2f(18.5,4.4);
    glVertex2f(18.8,4.4);
    glVertex2f(18.8,5.5);
    glVertex2f(18.5,5.5);
    glEnd();
glLineWidth(2.0f);
glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(18.5,4.4);
    glVertex2f(18.8,4.4);
    glVertex2f(18.8,5.5);
    glVertex2f(18.5,5.5);
    glEnd();
glLineWidth(2.5f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(18.7,4.2);
    glVertex2f(18.7,5.5);
    glEnd();
glBegin(GL_QUADS);
    glColor3ub(145,67,81);
    glVertex2f(19.4,4.4);
    glVertex2f(19.7,4.4);
    glVertex2f(19.7,5.5);
    glVertex2f(19.4,5.5);
    glEnd();
glLineWidth(2.0f);
glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(19.4,4.4);
    glVertex2f(19.7,4.4);
    glVertex2f(19.7,5.5);
    glVertex2f(19.4,5.5);
    glEnd();
glLineWidth(2.5f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(19.5,4.2);
    glVertex2f(19.5,5.5);
    glEnd();
//shoproof
    glBegin(GL_POLYGON);
    glColor3ub(240,189,55);
    glVertex2f(15.3,5.5);
    glVertex2f(20.4,5.5);
    glVertex2f(18.0,7.5);
    glEnd();
glLineWidth(2.0f);
glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(15.3,5.5);
    glVertex2f(20.4,5.5);
    glVertex2f(18.0,7.5);
    glEnd();
glLineWidth(2.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(18.0,7.5);
    glVertex2f(17.4,5.5);
    glEnd();
glBegin(GL_POLYGON);
    glColor3ub(255,162,69);
    glVertex2f(18.0,7.5);
    glVertex2f(18.6,6.78);
    glVertex2f(18.56,6.73);
    glVertex2f(19.2,6.3);
    glVertex2f(19.18,6.18);
    glVertex2f(20.4,5.5);
    glEnd();
glBegin(GL_POLYGON);
    glColor3ub(255,162,69);
    glVertex2f(16.5,5.5);
    glVertex2f(17.4,5.5);
    glVertex2f(18.0,7.5);
    glVertex2f(16.93,6.3);
    glVertex2f(17.25,6.27);
    glEnd();
    glBegin(GL_QUADS);
    glColor3ub(145,67,81);
    glVertex2f(16.3,2.6);
    glVertex2f(18.7,2.6);
    glVertex2f(18.7,4.2);
    glVertex2f(16.3,4.2);
    glEnd();
glBegin(GL_POLYGON);
    glColor3ub(94,44,71);
    glVertex2f(16.3,3.9);
    glVertex2f(18.7,3.5);
    glVertex2f(18.7,4.2);
    glVertex2f(16.3,4.2);
    glEnd();
glLineWidth(2.5f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(16.8,3.9);
    glVertex2f(18.5,3.9);
    glEnd();
glLineWidth(2.5f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(16.3,3.7);
    glVertex2f(18.5,3.7);
    glEnd();
glLineWidth(2.5f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(16.3,3.4);
    glVertex2f(16.9,3.4);
    glEnd();
glLineWidth(2.5f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(16.6,3.2);
    glVertex2f(18.4,3.2);
    glEnd();



    }
//S4_BL
void S4_BoardLighted(){
            //board
    glBegin(GL_POLYGON);
    glColor3ub(148,89,78);
    glVertex2f(15.5, 7);
    glVertex2f(15.9994284432612, 7.2448951300453);
    glVertex2f(15.5, 7.5);
    glVertex2f(20, 7.5);
    glVertex2f(19.5063925100027, 7.2507400701565);
    glVertex2f(20, 7);
    glEnd();

    glBegin(GL_TRIANGLES);
    glColor3ub(40,36,66);
    glVertex2f(15.5, 7);
    glVertex2f(15.9994284432612, 7.2448951300453);
    glVertex2f(15.5, 7.5);
    glEnd();

    glBegin(GL_LINE_LOOP);
    glColor3ub(1,31,75);
    glVertex2f(15.5, 7);
    glVertex2f(20.0,7.0);
    glVertex2f(19.5063925100027, 7.2507400701565);
    glVertex2f(20.0,7.5);
    glVertex2f(15.5,7.5);
    glVertex2f(15.9994284432612, 7.2448951300453);
    glEnd();

    //J
    glColor3ub(118,165,175);
    glLineWidth(2.5f);
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(16.6, 7.4);
    glVertex2f(16.8952361438692, 7.3999108642741);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(16.79, 7.4);
    glVertex2f(16.7930362584353, 7.1280055342436);
    glEnd();
    drawTiltedHollowSemiCircle(16.6928682149093, 7.1268464103162,0.094,0,false);

    //U
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(17.1101901960062, 7.389376724065);
    glVertex2f(17.1111035674595, 7.1723551347304);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(17.4032373611813, 7.3829552257626);
    glVertex2f(17.4038160747662, 7.1723551347304);
    glEnd();
    drawTiltedHollowSemiCircle(17.25000001212, 7.1713417437256,0.136,0,false);

    //I
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(17.7440680569499, 7.0593799462696);
    glVertex2f(17.7468819698948, 7.4001974609834);
    glEnd();

    //C
    drawTiltedHollowSemiCircle(18.214610197932, 7.2409438074779,0.15,90,true);

    //E
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(18.6037154343155, 7.0956740391545);
    glVertex2f(18.6037123826749, 7.4178389132929);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(18.6037123826749, 7.4178389132929);
    glVertex2f(18.8684100399316, 7.4161715155384);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(18.6,7.3);
    glVertex2f(18.7929831658903, 7.3);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(18.6037154343155, 7.0956740391545);
    glVertex2f(18.8774919210494, 7.0937733372579);
    glEnd();

    //Middle juicebox
    glColor3ub(32,155,56);
    drawTiltedHollowSemiCircle(17.8845747302662, 7.7358241081472,0.20,75.68,false);
    drawTiltedHollowSemiCircle(17.415006140826, 7.7292104660425,0.20,284.32,false);
    glColor3ub(223,0,80);
    drawHollowEllipse(17.6410082807829, 7.9809895131394,0.28,0.09,200);
    glColor3ub(255,190,0);
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(17.7604018291005, 7.88084897367);
    glVertex2f(17.982767272757, 8.4830308250124);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(18.1263472166237, 8.5255143042144);
    glVertex2f(17.982767272757, 8.4830308250124);
    glEnd();

    glBegin(GL_LINES);
    glColor3ub(0,200,244);
    glVertex2f(17.6119793380043, 7.9461199278198);
    glVertex2f(17.4, 8.4);
    glEnd();

    glBegin(GL_LINES);
    glColor3ub(255,229,155);
    glVertex2f(17.4026641732678, 8.1516293622883);
    glVertex2f(17.6140551908777, 8.2398531094087);
    glEnd();
    glBegin(GL_LINES);
    glColor3ub(255,229,155);
    glVertex2f(17.44,8.3);
    glVertex2f(17.6140551908777, 8.2398531094087);
    glEnd();
    glBegin(GL_LINES);
    glColor3ub(255,229,155);
    glVertex2f(17.44,8.3);
    glVertex2f(17.4026641732678, 8.1516293622883);
    glEnd();

    //Left Juicebox
    glLineWidth(2.0);
    glColor3ub(240,105,105);
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.9,7.5);
    glVertex2f(16.7271061170485, 7.8207220715546);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.76980729577967, 8.05483720488847);
    glVertex2f(16.7271061170485, 7.8207220715546);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.76980729577967, 8.05483720488847);
    glVertex2f(16.4884384315571, 7.9266890093019);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.6764869601934, 7.7990281471881);
    glVertex2f(16.4884384315571, 7.9266890093019);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.6764869601934, 7.7990281471881);
    glVertex2f(16.8051177632278, 7.4991236613211);
    glEnd();
    glLineWidth(1.7);
    glColor3ub(254,198,1);
    drawHollowCircle(16.4884384315571, 7.9266890093019,0.077,200);
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.4884384315571, 7.9266890093019);
    glVertex2f(16.5,8.00);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.4884384315571, 7.9266890093019);
    glVertex2f(16.45,8.00);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.4884384315571, 7.9266890093019);
    glVertex2f(16.41,8.00);
    glEnd();
     glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.4884384315571, 7.9266890093019);
    glVertex2f(16.435,8.00);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.4884384315571, 7.9266890093019);
    glVertex2f(16.435,7.86);
    glEnd();
    //Right JuiceBox
    glColor3ub(255,59,63);
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(18.5988945066304, 7.4978820014475);
    glVertex2f(18.5128260477821, 8.0056859086526);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(18.885789369458198, 8.005685908652586);
    glVertex2f(18.5128260477821, 8.0056859086526);
    glEnd();
    //glColor3ub(122,44,44);
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(18.885789369458198, 8.005685908652586);
    glVertex2f(18.79,7.50);
    glEnd();
    glColor3ub(0,174,239);
    glBegin(GL_LINE_LOOP);
    glVertex2f(18.5995629787153, 7.6179796988072);
    glVertex2f(18.6558583941584, 7.5986281497487);
    glVertex2f(18.6763561195939, 7.6528052273745);
    glVertex2f(18.6153960643087, 7.6707566507851);
    glEnd();
    glBegin(GL_LINES);
    glColor3ub(255,145,93);
    glVertex2f(18.6030814421805, 7.8836236904291);
    glVertex2f(18.8025499895912, 7.8825958906754);
    glEnd();
    glColor3ub(229,52,132);
    glBegin(GL_LINES);
    //glColor3ub(255,145,93);
    glVertex2f(18.7145588013168, 7.7440219423338);
    glVertex2f(18.8680683761428, 8.2142973064835);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,145,93);
    glVertex2f(18.9484781534327, 8.2289172659907);
    glVertex2f(18.8680683761428, 8.2142973064835);
    glEnd();

}
//S4_BNL
void S4_BoardNotLighted(){
        glBegin(GL_POLYGON);
    glColor3ub(148,89,78);
    glVertex2f(15.5, 7);
    glVertex2f(15.9994284432612, 7.2448951300453);
    glVertex2f(15.5, 7.5);
    glVertex2f(20, 7.5);
    glVertex2f(19.5063925100027, 7.2507400701565);
    glVertex2f(20, 7);
    glEnd();

    glBegin(GL_TRIANGLES);
    glColor3ub(40,36,66);
    glVertex2f(15.5, 7);
    glVertex2f(15.9994284432612, 7.2448951300453);
    glVertex2f(15.5, 7.5);
    glEnd();

    glBegin(GL_LINE_LOOP);
    glColor3ub(1,31,75);
    glVertex2f(15.5, 7);
    glVertex2f(20.0,7.0);
    glVertex2f(19.5063925100027, 7.2507400701565);
    glVertex2f(20.0,7.5);
    glVertex2f(15.5,7.5);
    glVertex2f(15.9994284432612, 7.2448951300453);
    glEnd();

    //J
    glColor3ub(255,255,255);
    glLineWidth(2.5f);
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(16.6, 7.4);
    glVertex2f(16.8952361438692, 7.3999108642741);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(16.79, 7.4);
    glVertex2f(16.7930362584353, 7.1280055342436);
    glEnd();
    drawTiltedHollowSemiCircle(16.6928682149093, 7.1268464103162,0.094,0,false);

    //U
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(17.1101901960062, 7.389376724065);
    glVertex2f(17.1111035674595, 7.1723551347304);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(17.4032373611813, 7.3829552257626);
    glVertex2f(17.4038160747662, 7.1723551347304);
    glEnd();
    drawTiltedHollowSemiCircle(17.25000001212, 7.1713417437256,0.136,0,false);

    //I
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(17.7440680569499, 7.0593799462696);
    glVertex2f(17.7468819698948, 7.4001974609834);
    glEnd();

    //C
    drawTiltedHollowSemiCircle(18.214610197932, 7.2409438074779,0.15,90,true);

    //E
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(18.6037154343155, 7.0956740391545);
    glVertex2f(18.6037123826749, 7.4178389132929);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(18.6037123826749, 7.4178389132929);
    glVertex2f(18.8684100399316, 7.4161715155384);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(18.6,7.3);
    glVertex2f(18.7929831658903, 7.3);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(18.6037154343155, 7.0956740391545);
    glVertex2f(18.8774919210494, 7.0937733372579);
    glEnd();

    //Middle juicebox
    //glColor3ub(32,155,56);
    drawTiltedHollowSemiCircle(17.8845747302662, 7.7358241081472,0.20,75.68,false);
    drawTiltedHollowSemiCircle(17.415006140826, 7.7292104660425,0.20,284.32,false);
    //glColor3ub(223,0,80);
    drawHollowEllipse(17.6410082807829, 7.9809895131394,0.28,0.09,200);
    //glColor3ub(255,190,0);
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(17.7604018291005, 7.88084897367);
    glVertex2f(17.982767272757, 8.4830308250124);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(10,117,173);
    glVertex2f(18.1263472166237, 8.5255143042144);
    glVertex2f(17.982767272757, 8.4830308250124);
    glEnd();

    glBegin(GL_LINES);
    //glColor3ub(0,200,244);
    glVertex2f(17.6119793380043, 7.9461199278198);
    glVertex2f(17.4, 8.4);
    glEnd();

    glBegin(GL_LINES);
   // glColor3ub(255,229,155);
    glVertex2f(17.4026641732678, 8.1516293622883);
    glVertex2f(17.6140551908777, 8.2398531094087);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(17.44,8.3);
    glVertex2f(17.6140551908777, 8.2398531094087);
    glEnd();
    glBegin(GL_LINES);
   // glColor3ub(255,229,155);
    glVertex2f(17.44,8.3);
    glVertex2f(17.4026641732678, 8.1516293622883);
    glEnd();

    //Left Juicebox
    glLineWidth(2.0);
    //glColor3ub(240,105,105);
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.9,7.5);
    glVertex2f(16.7271061170485, 7.8207220715546);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.76980729577967, 8.05483720488847);
    glVertex2f(16.7271061170485, 7.8207220715546);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.76980729577967, 8.05483720488847);
    glVertex2f(16.4884384315571, 7.9266890093019);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.6764869601934, 7.7990281471881);
    glVertex2f(16.4884384315571, 7.9266890093019);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.6764869601934, 7.7990281471881);
    glVertex2f(16.8051177632278, 7.4991236613211);
    glEnd();
    glLineWidth(1.7);
    //glColor3ub(254,198,1);
    drawHollowCircle(16.4884384315571, 7.9266890093019,0.077,200);
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.4884384315571, 7.9266890093019);
    glVertex2f(16.5,8.00);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.4884384315571, 7.9266890093019);
    glVertex2f(16.45,8.00);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.4884384315571, 7.9266890093019);
    glVertex2f(16.41,8.00);
    glEnd();
     glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.4884384315571, 7.9266890093019);
    glVertex2f(16.435,8.00);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(16.4884384315571, 7.9266890093019);
    glVertex2f(16.435,7.86);
    glEnd();

    //Right juicebox
    //Right JuiceBox
   // glColor3ub(255,59,63);
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(18.5988945066304, 7.4978820014475);
    glVertex2f(18.5128260477821, 8.0056859086526);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(18.885789369458198, 8.005685908652586);
    glVertex2f(18.5128260477821, 8.0056859086526);
    glEnd();
    //glColor3ub(122,44,44);
    glBegin(GL_LINES);
    //glColor3ub(255,229,155);
    glVertex2f(18.885789369458198, 8.005685908652586);
    glVertex2f(18.79,7.50);
    glEnd();
   //glColor3ub(0,174,239);
    glBegin(GL_LINE_LOOP);
    glVertex2f(18.5995629787153, 7.6179796988072);
    glVertex2f(18.6558583941584, 7.5986281497487);
    glVertex2f(18.6763561195939, 7.6528052273745);
    glVertex2f(18.6153960643087, 7.6707566507851);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,145,93);
    glVertex2f(18.6030814421805, 7.8836236904291);
    glVertex2f(18.8025499895912, 7.8825958906754);
    glEnd();
    //glColor3ub(229,52,132);
    glBegin(GL_LINES);
    //glColor3ub(255,145,93);
    glVertex2f(18.7145588013168, 7.7440219423338);
    glVertex2f(18.8680683761428, 8.2142973064835);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(255,145,93);
    glVertex2f(18.9484781534327, 8.2289172659907);
    glVertex2f(18.8680683761428, 8.2142973064835);
    glEnd();

}

//S4_SB
void S4_SurfBoard(){
    //Board1
glColor3ub(135,206,235);
drawTiltedFilledEllipse(14.6,3.8,1.39,0.33,92,100);
glColor3ub(0,0,0);
glLineWidth(2.0f);
drawTiltedHollowEllipse(14.6,3.8,1.39,0.33,92,100);
glLineWidth(5.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(14.23,3.8);
    glVertex2f(14.60,4.2);
    glEnd();
glLineWidth(5.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(14.95,3.8);
    glVertex2f(14.55,4.2);
    glEnd();
    //Board2
glColor3ub(106,139,65);
drawTiltedFilledEllipse(15.5,3.8,1.37,0.34,73,100);
glColor3ub(0,0,0);
glLineWidth(2.0f);
drawTiltedHollowEllipse(15.5,3.8,1.37,0.34,73,100);
glLineWidth(8.5f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(15.9,5.1);
    glVertex2f(15.1,2.5);
    glEnd();
}
//S4_CUCS
S4_ChairAndUmbrellaAndCoconutTreeAndSandcastle(){
//chair
glLineWidth(4.5f);
    glBegin(GL_LINES);
    glColor3ub(117, 78, 26);
    glVertex2f(11.4239395465962, 8.4855458649584);
    glVertex2f(13.0379068825479, 6.1389266605542);
    glEnd();
    glBegin(GL_LINES);
    glColor3ub(117, 78, 26);
    glVertex2f(11.3665803769707, 6.5159942258368);
    glVertex2f(13.1698688793076, 6.9437187299004);
    glEnd();
    glBegin(GL_LINES);
    glColor3ub(117, 78, 26);
    glVertex2f(11.96,7.70);
    glVertex2f(11.67,6.59);
    glEnd();
    glBegin(GL_LINES);
    glColor3ub(117, 78, 26);
    glVertex2f(12.4570190117071, 8.6764894647536);
    glVertex2f(14.046817395061, 6.3529379813903);
    glEnd();
    glBegin(GL_POLYGON);
    glColor3ub(245,245,245);
    glVertex2f(12.7693418226509, 6.9356405684846);
    glVertex2f(13.8613893118479, 7.1739842665236);
    glVertex2f(13.1290241306007, 7.4339955734753);
    glVertex2f(12.7830155834126, 7.885120325181);
    glVertex2f(12.4804196722334, 8.530046964159);
    glVertex2f(11.6765537667773, 8.3833337951024);
    glVertex2f(12.5006634721342, 7.1046479180032);
    glEnd();
    glLineWidth(2.0f);
    glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(12.7693418226509, 6.9356405684846);
    glVertex2f(13.8613893118479, 7.1739842665236);
    glVertex2f(13.1290241306007, 7.4339955734753);
    glVertex2f(12.7830155834126, 7.885120325181);
    glVertex2f(12.4804196722334, 8.530046964159);
    glVertex2f(11.6765537667773, 8.3833337951024);
    glVertex2f(12.5006634721342, 7.1046479180032);
    glEnd();
    glLineWidth(3.0f);
    glColor3ub(190, 49, 68);
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(12.3484709188647, 8.4931151567221);
    glVertex2f(12.6381134177385, 7.8649747977188);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(13, 7.4);
    glVertex2f(12.6381134177385, 7.8649747977188);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(12.2053945037584, 8.4651978074331);
    glVertex2f(12.5089956772767, 7.8230987737853);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(12.8754108866953, 7.35199350453281);
    glVertex2f(12.5089956772767, 7.8230987737853);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(12.8754108866953, 7.3519935045328);
    glVertex2f(13.47,7.08);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(13, 7.4);
    glVertex2f(13.6361586548215, 7.1495927221873);
    glEnd();

    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(12.06,8.45);
    glVertex2f(12.3722929545184, 7.7845682173285);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(12.7212598206313, 7.3173563800534);
    glVertex2f(12.3722929545184, 7.7845682173285);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(12.7212598206313, 7.3173563800534);
    glVertex2f(13.23,7.035);
    glEnd();

    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(11.9325633517603, 8.4094074103283);
    glVertex2f(12.2446854145186, 7.7653485080865);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(12.580517599242, 7.2908700085692);
    glVertex2f(12.2446854145186, 7.7653485080865);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(12.580517599242, 7.2908700085692);
    glVertex2f(13.0,7.0);
    glEnd();

        //ball
        glColor3ub(0,0,0);
        glLineWidth(4.5);
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(10.9360040103807, 6.6430865022044);
    glVertex2f(10.4770015210857, 9.1599652146635);
    glEnd();
        glColor3ub(41, 115, 178);
    drawFilledCircle(10.7096480354569, 6.385509013498,0.57,100);
    glColor3ub(0,0,0);
    glLineWidth(2.0f);
    drawHollowCircle(10.7096480354569, 6.385509013498,0.57,100);
    glColor3ub(154, 203, 208);
    drawFilledCircle(10.9360040103807, 6.6430865022044,0.18,100);
    glColor3ub(0,0,0);
    drawHollowCircle(10.9360040103807, 6.6430865022044,0.18,100);
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(11.0506572679101, 6.4777100155275);
    glVertex2f(11.06,5.94);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(10.874092157308, 6.475955111828);
    glVertex2f(10.48,5.86);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(10.78,6.55);
    glVertex2f(10.19,6.14);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(10.17,6.57);
    glVertex2f(10.75,6.65);
    glEnd();

    //umbrella

    glColor3ub(231, 210, 131);
    //drawTiltedSemicircle(10.4195426413345, 8.8530412884725,1.46,10,true);
    //drawTiltedSemicircle(10.4195426413345, 8.8530412884725,1.46,10,true);
    drawSemiCircle(10.4195426413345, 8.8530412884725,1.46,10);
    glBegin(GL_TRIANGLES);
    glVertex2f(9.0102733829077, 8.6609783640447);
    glVertex2f(11.986385589037, 9.1114204456755);
    glVertex2f(10.19,10.36);
    glEnd();
    glColor3ub(0,0,0);
    glBegin(GL_LINE_LOOP);
    glVertex2f(9.0102733829077, 8.6609783640447);
    glVertex2f(11.986385589037, 9.1114204456755);
    glVertex2f(10.19,10.36);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(10.19,10.36);
    glVertex2f(9.76,9.05);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(10.19,10.36);
    glVertex2f(10.1679968143675, 9.131967211491);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(10.19,10.36);
    glVertex2f(10.92,9.241);
    glEnd();
    glBegin(GL_LINES);
    //glColor3ub(117, 78, 26);
    glVertex2f(10.19,10.36);
    glVertex2f(11.32,9.27);
    glEnd();
    glColor3ub(0,174,239);
    drawTiltedFilledEllipse(10.4770015210857, 8.919652146635,1.48,0.29,10,100);
    glColor3ub(0,0,0);
    drawTiltedHollowEllipse(10.4770015210857, 8.919652146635,1.48,0.29,10,100);


   // coconut tree
   //trunk
   glBegin(GL_POLYGON);
glColor3ub(117,78,26);
    glVertex2f(20.9314044950766, 4.8812780159034);
    glVertex2f(21.2717574464453, 5.0113205478211);
    glVertex2f(21.3403262092921, 5.6524023171941);
    glVertex2f(21.0411079684595, 5.6640224430516);
    glEnd();
    glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
    glVertex2f(20.9314044950766, 4.8812780159034);
    glVertex2f(21.2717574464453, 5.0113205478211);
    glVertex2f(21.3403262092921, 5.6524023171941);
    glVertex2f(21.0411079684595, 5.6640224430516);
    glEnd();
glBegin(GL_POLYGON);
glColor3ub(117,78,26);
    glVertex2f(20.9888174021004, 5.6698325059804);
    glVertex2f(21.3897117441867, 5.6524023171941);
    glVertex2f(21.369376523936, 6.1549727605341);
    glVertex2f(21.1021136292118, 6.146257666141);
    glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
    glVertex2f(20.9888174021004, 5.6698325059804);
    glVertex2f(21.3897117441867, 5.6524023171941);
    glVertex2f(21.369376523936, 6.1549727605341);
    glVertex2f(21.1021136292118, 6.146257666141);
    glEnd();
glBegin(GL_POLYGON);
glColor3ub(117,78,26);
    glVertex2f(21.0475843678701, 6.1465831108816);
    glVertex2f(21.369376523936, 6.1549727605341);
    glVertex2f(21.3635116349489, 6.5939361210651);
    glVertex2f(21.1208794938324, 6.5888812847918);
    glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
    glVertex2f(21.0475843678701, 6.1465831108816);
    glVertex2f(21.369376523936, 6.1549727605341);
    glVertex2f(21.3635116349489, 6.5939361210651);
    glVertex2f(21.1208794938324, 6.5888812847918);
    glEnd();
glBegin(GL_POLYGON);
glColor3ub(117,78,26);
    glVertex2f(21.1208794938324, 6.5888812847918);
    glVertex2f(21.3635116349489, 6.5939361210651);
    glVertex2f(21.4108143010743, 6.6848948690993);
    glVertex2f(21.2977242210149, 7.0235776660512);
    glVertex2f(21.0668313334531, 7.0070853169397);
    glVertex2f(21.03384663523, 6.660745985597);
    glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
    glVertex2f(21.1208794938324, 6.5888812847918);
    glVertex2f(21.3635116349489, 6.5939361210651);
    glVertex2f(21.4108143010743, 6.6848948690993);
    glVertex2f(21.2977242210149, 7.0235776660512);
    glVertex2f(21.0668313334531, 7.0070853169397);
    glVertex2f(21.03384663523, 6.660745985597);
    glEnd();
glBegin(GL_POLYGON);
glColor3ub(117,78,26);
    glVertex2f(21.0668313334531, 7.0070853169397);
    glVertex2f(21.2977242210149, 7.0235776660512);
    glVertex2f(21.1848670371722, 7.4308280925168);
    glVertex2f(21.0465978761727, 7.408490586701);
    glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
    glVertex2f(21.0668313334531, 7.0070853169397);
    glVertex2f(21.2977242210149, 7.0235776660512);
    glVertex2f(21.1848670371722, 7.4308280925168);
    glVertex2f(21.0465978761727, 7.408490586701);
    glEnd();
glBegin(GL_POLYGON);
glColor3ub(117,78,26);
    glVertex2f(21, 7.4);
    glVertex2f(21.1848670371722, 7.4308280925168);
    glVertex2f(21.1066857668171, 7.8403490324723);
    glVertex2f(20.9614919790147, 7.8068427737486);
    glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
    glVertex2f(21, 7.4);
    glVertex2f(21.1848670371722, 7.4308280925168);
    glVertex2f(21.1066857668171, 7.8403490324723);
    glVertex2f(20.9614919790147, 7.8068427737486);
    glEnd();
    //leaf1
glBegin(GL_POLYGON);
glColor3ub(93,135,54);
    glVertex2f(20.9614919790147, 7.8068427737486);
    glVertex2f(20.8384869644058, 7.3476741106579);
    glVertex2f(20.6851390866319, 7.4359653130125);
    glVertex2f(20.7548426674382, 7.2919112460128);
    glVertex2f(20.5364381142452, 6.9480402473684);
    glVertex2f(20.4527938172776, 7.082800503593);
    glVertex2f(20.4481469118905, 6.901571193497);
    glVertex2f(20.2, 6.6);
    glVertex2f(20.0927879029379, 7.0177840478613);
    glVertex2f(20.1549785230366, 7.1378762797762);
    glVertex2f(20.0691983573832, 7.1207202466455);
    glVertex2f(20.1858853647434, 7.4785341921949);
    glVertex2f(20.3605471598586, 7.5470290138087);
    glVertex2f(20.2543801863572, 7.5778516835349);
    glVertex2f(20.6756233392821, 7.8723794164742);
    glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
    glVertex2f(20.9614919790147, 7.8068427737486);
    glVertex2f(20.8384869644058, 7.3476741106579);
    glVertex2f(20.6851390866319, 7.4359653130125);
    glVertex2f(20.7548426674382, 7.2919112460128);
    glVertex2f(20.5364381142452, 6.9480402473684);
    glVertex2f(20.4527938172776, 7.082800503593);
    glVertex2f(20.4481469118905, 6.901571193497);
    glVertex2f(20.2, 6.6);
    glVertex2f(20.0927879029379, 7.0177840478613);
    glVertex2f(20.1549785230366, 7.1378762797762);
    glVertex2f(20.0691983573832, 7.1207202466455);
    glVertex2f(20.1858853647434, 7.4785341921949);
    glVertex2f(20.3605471598586, 7.5470290138087);
    glVertex2f(20.2543801863572, 7.5778516835349);
    glVertex2f(20.6756233392821, 7.8723794164742);
    glEnd();
    //drawTiltedUpperHollowEllipse(20.4371637774493, 8.3926442390562,0.65,0.043,330,100);
    //leaf2
    glBegin(GL_POLYGON);
glColor3ub(93,135,54);
    glVertex2f(20.6756233392821, 7.8723794164742);
    glVertex2f(20.2732228529147, 7.7500212968478);
    glVertex2f(20.1651748286226, 7.613786831436);
    glVertex2f(19.823888735685, 7.5658643090008);
    glVertex2f(19.3619976405104, 7.5944770317108);
    glVertex2f(19.5868118903741, 7.9460047678614);
    glVertex2f(19.7748383538966, 7.9664424269399);
    glVertex2f(19.7094378448453, 8.0400179996226);
    glVertex2f(20.0977533673372, 8.1830816131723);
    glVertex2f(20.2489920445183, 8.0808933177796);
    glVertex2f(20.2612546399654, 8.1912566768037);
    glVertex2f(20.72,8.00);
    glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
    glVertex2f(20.6756233392821, 7.8723794164742);
    glVertex2f(20.2732228529147, 7.7500212968478);
    glVertex2f(20.1651748286226, 7.613786831436);
    glVertex2f(19.823888735685, 7.5658643090008);
    glVertex2f(19.3619976405104, 7.5944770317108);
    glVertex2f(19.5868118903741, 7.9460047678614);
    glVertex2f(19.7748383538966, 7.9664424269399);
    glVertex2f(19.7094378448453, 8.0400179996226);
    glVertex2f(20.0977533673372, 8.1830816131723);
    glVertex2f(20.2489920445183, 8.0808933177796);
    glVertex2f(20.2612546399654, 8.1912566768037);
    glVertex2f(20.72,8.00);
    glEnd();
 //leaf3
 glBegin(GL_POLYGON);
glColor3ub(91,145,59);
    glVertex2f(20.72,8.00);
    glVertex2f(20.3475097343154, 8.1770364827691);
    glVertex2f(20.3872896010564, 8.3325395982114);
    glVertex2f(20.2137047280045, 8.307225137558);
    glVertex2f(19.982258230602, 8.4952754166976);
    glVertex2f(20.0256544488649, 8.6146150169208);
    glVertex2f(19.8665349819007, 8.56760244713591);
    glVertex2f(19.6459375390639, 8.8135143506261);
    glVertex2f(20.0256544488649, 8.8858413810644);
    glVertex2f(20.1341449945224, 8.7592690777974);
    glVertex2f(20.1196795884347, 8.8677596234548);
    glVertex2f(20.448767576929, 8.7448036717097);
    glVertex2f(20.5210946073673, 8.5712187986578);
    glVertex2f(20.5500254195426, 8.690558398881);
    glVertex2f(20.8718807049931, 8.3578540588648);
    glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
    glVertex2f(20.72,8.00);
    glVertex2f(20.3475097343154, 8.1770364827691);
    glVertex2f(20.3872896010564, 8.3325395982114);
    glVertex2f(20.2137047280045, 8.307225137558);
    glVertex2f(19.982258230602, 8.4952754166976);
    glVertex2f(20.0256544488649, 8.6146150169208);
    glVertex2f(19.8665349819007, 8.56760244713591);
    glVertex2f(19.6459375390639, 8.8135143506261);
    glVertex2f(20.0256544488649, 8.8858413810644);
    glVertex2f(20.1341449945224, 8.7592690777974);
    glVertex2f(20.1196795884347, 8.8677596234548);
    glVertex2f(20.448767576929, 8.7448036717097);
    glVertex2f(20.5210946073673, 8.5712187986578);
    glVertex2f(20.5500254195426, 8.690558398881);
    glVertex2f(20.8718807049931, 8.3578540588648);
    glEnd();
//leaf4
glBegin(GL_POLYGON);
glColor3ub(91,145,59);
    glVertex2f(20.8275160325479, 7.9986474595247);
    glVertex2f(20.9912203052163, 8.6399294775742);
    glVertex2f(21.0599309841326, 8.5531370410482);
    glVertex2f(21.0771057814837, 8.7059307652272);
    glVertex2f(21.583761947731, 9.0364764993565);
    glVertex2f(21.5864713389943, 8.9281008488223);
    glVertex2f(21.7002657720552, 9.0500234556732);
    glVertex2f(22.1093838528218, 9.0229295430397 );
    glVertex2f(21.8953419430168, 8.746571634177);
    glVertex2f(21.7327784672155, 8.7221871128073);
    glVertex2f(21.8194789876428, 8.6788368525937);
    glVertex2f(21.56,8.41);
    glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0 );
    glVertex2f(20.8275160325479, 7.9986474595247);
    glVertex2f(20.9912203052163, 8.6399294775742);
    glVertex2f(21.0599309841326, 8.5531370410482);
    glVertex2f(21.0771057814837, 8.7059307652272);
    glVertex2f(21.583761947731, 9.0364764993565);
    glVertex2f(21.5864713389943, 8.9281008488223);
    glVertex2f(21.7002657720552, 9.0500234556732);
    glVertex2f(22.1093838528218, 9.0229295430397 );
    glVertex2f(21.8953419430168, 8.746571634177);
    glVertex2f(21.7327784672155, 8.7221871128073);
    glVertex2f(21.8194789876428, 8.6788368525937);
    glVertex2f(21.56,8.41);
    glEnd();
//leaf5
glBegin(GL_POLYGON);
glColor3ub(91,145,59);
    glVertex2f(21.04,8.12);
    glVertex2f(21.3615918641359, 8.3780944223613);
    glVertex2f(21.3724294291893, 8.2615905980371);
    glVertex2f(21.4726769059334, 8.4051883349949);
    glVertex2f(21.9332734207037, 8.3699662485712);
    glVertex2f(21.8926325517534, 8.2588812067737);
    glVertex2f(22.0335208974479, 8.3184878145675);
    glVertex2f(22.4534765432678, 8.0665144270755);
    glVertex2f(22.003717593551, 7.8822758211674);
    glVertex2f(21.7815475099559, 7.9662669503314);
    glVertex2f(21.8763762041733, 7.8660194735873);
    glVertex2f(21.2586349961284, 7.8470537347438);
    glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
    glVertex2f(21.04,8.12);
    glVertex2f(21.3615918641359, 8.3780944223613);
    glVertex2f(21.3724294291893, 8.2615905980371);
    glVertex2f(21.4726769059334, 8.4051883349949);
    glVertex2f(21.9332734207037, 8.3699662485712);
    glVertex2f(21.8926325517534, 8.2588812067737);
    glVertex2f(22.0335208974479, 8.3184878145675);
    glVertex2f(22.4534765432678, 8.0665144270755);
    glVertex2f(22.003717593551, 7.8822758211674);
    glVertex2f(21.7815475099559, 7.9662669503314);
    glVertex2f(21.8763762041733, 7.8660194735873);
    glVertex2f(21.2586349961284, 7.8470537347438);
    glEnd();
//Leaf6
glBegin(GL_POLYGON);
glColor3ub(91,145,59);
    glVertex2f(21.2586349961284, 7.8470537347438);
    glVertex2f(21.7193064404776, 7.7411517518396);
    glVertex2f(21.5880346374726, 7.6528416298181);
    glVertex2f(21.7813622018981, 7.6409078295449);
    glVertex2f(22.0033308869792, 7.3401760626609);
    glVertex2f(21.8434179633186, 7.3091481819506);
    glVertex2f(22.0271984875255, 7.2637997409125);
    glVertex2f(22.0333422743227, 6.8035003617872);
    glVertex2f(21.6248364759688, 6.9523949985704);
    glVertex2f(21.6211307367896, 7.1526534474795);
    glVertex2f(21.528830835878, 7.0010178959818);
    glVertex2f(21.3618119675617, 7.2295700315725);
    glVertex2f(21.4, 7.4);
    glVertex2f(21.2826977667803, 7.2867080654702);
    glVertex2f(21.2035835659989, 7.6954647695074);
    glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
    glVertex2f(21.2586349961284, 7.8470537347438);
    glVertex2f(21.7193064404776, 7.7411517518396);
    glVertex2f(21.5880346374726, 7.6528416298181);
    glVertex2f(21.7813622018981, 7.6409078295449);
    glVertex2f(22.0033308869792, 7.3401760626609);
    glVertex2f(21.8434179633186, 7.3091481819506);
    glVertex2f(22.0271984875255, 7.2637997409125);
    glVertex2f(22.0333422743227, 6.8035003617872);
    glVertex2f(21.6248364759688, 6.9523949985704);
    glVertex2f(21.6211307367896, 7.1526534474795);
    glVertex2f(21.528830835878, 7.0010178959818);
    glVertex2f(21.3618119675617, 7.2295700315725);
    glVertex2f(21.4, 7.4);
    glVertex2f(21.2826977667803, 7.2867080654702);
    glVertex2f(21.2035835659989, 7.6954647695074);
    glEnd();

    drawTiltedUpperHollowEllipse(20.4371637774493, 8.3926442390562,0.65,0.043,330,100);
    drawTiltedUpperHollowEllipse(20.2371637774493, 7.8726442390562,0.65,0.043,20,100);
    drawTiltedUpperHollowEllipse(20.5371637774493, 7.4726442390562,0.65,0.043,55,100);
    drawLowerHollowTiltedEllipse(21.5371637774493, 8.6626442390562,0.65,0.043,-140,100);
    //coconuts
    glColor3ub(169,196,108);
    drawFilledCircle(21.1, 7.9,0.21,100);
    glColor3ub(0,0,0);
    drawHollowCircle(21.1, 7.9,0.21,100);
    glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
    glVertex2f(21.0774445888544, 8.2045244731302);
    glVertex2f(21.3027835431897, 8.1961785859326);
    glVertex2f(21.3862424151657, 8.0897685241632);
    glVertex2f(21.1984599532197, 8.0918549959626);
    glVertex2f(21.2360164456089, 7.9708396315973);
    glVertex2f(21.1129146094442, 8.0000502367889);
    glVertex2f(21.0419745682646, 7.9124184212141);
    glVertex2f(20.9731209988844, 8.016742011184);
    glEnd();


    glColor3ub(169,196,108);
    drawFilledCircle(20.8384869644058, 7.8541867978503,0.21,100);
    glColor3ub(0,0,0);
    drawHollowCircle(20.8384869644058, 7.8541867978503,0.21,100);
     /*glBegin(GL_POLYGON);
glColor3ub(128,158,60);
    glVertex2f(20.63,7.84);
    glVertex2f(20.7828560171183, 7.9399756214687);
    glVertex2f(20.8325109258481, 7.8274348085045);
    glVertex2f(20.8946775509294, 7.945661462171);
    glVertex2f(21.03,7.85);
    glVertex2f(20.9948085744334, 7.9812918194247);
    glVertex2f(20.8338979379155, 8.0673878961707);
    glEnd();*/
    glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
    glVertex2f(20.63,7.84);
    glVertex2f(20.7828560171183, 7.9399756214687);
    glVertex2f(20.8325109258481, 7.8274348085045);
    glVertex2f(20.8946775509294, 7.945661462171);
    glVertex2f(21.03,7.85);
    glVertex2f(20.9948085744334, 7.9812918194247);
    glVertex2f(20.8338979379155, 8.0673878961707);
    glVertex2f(20.7, 8);
    glEnd();




    //grass
   glBegin(GL_POLYGON);
glColor3ub(91,145,59);
    glVertex2f(20.5067351105311, 4.6688720120123);
    glVertex2f(21.5963494182972, 4.6605543455408);
    glVertex2f(21.4507578090349, 4.7392389240222);
    glVertex2f(21.4719784016471, 4.8835389537852);
    glVertex2f(21.3404107274514, 4.7944124648139);
    glVertex2f(21.3596539339446, 4.91040235847);
    glVertex2f(21.2619911700565, 4.8518047001371);
    glVertex2f(21.2717574464453, 5.0113205478211);
    glVertex2f(21.1349805849213, 4.8477768386389);
    glVertex2f(21.1913421695302, 5.1123295448599);
    glVertex2f(21.0185439689755, 4.8632795662984);
    glVertex2f(20.8123151149309, 5.24286021069932);
    glVertex2f(20.8691027703924, 4.9290336936749);
    glVertex2f(20.7704715793276, 4.9768548772215);
    glVertex2f(20.8302480587608, 4.8483354464401);
    glVertex2f(20.6748292122345, 4.8871901580717);
    glVertex2f(20.7166727478377, 4.8094807348085);
    glVertex2f(20.6, 4.8);
    glVertex2f(20.6479297964895, 4.734760135517);
    glEnd();
    glLineWidth(1.5);
    glBegin(GL_LINE_LOOP);

glColor3ub(0,0,0);
    glVertex2f(20.5067351105311, 4.6688720120123);
    glVertex2f(21.5963494182972, 4.6605543455408);
    glVertex2f(21.4507578090349, 4.7392389240222);
    glVertex2f(21.4719784016471, 4.8835389537852);
    glVertex2f(21.3404107274514, 4.7944124648139);
    glVertex2f(21.3596539339446, 4.91040235847);
    glVertex2f(21.2619911700565, 4.8518047001371);
    glVertex2f(21.2717574464453, 5.0113205478211);
    glVertex2f(21.1349805849213, 4.8477768386389);
    glVertex2f(21.1913421695302, 5.1123295448599);
    glVertex2f(21.0185439689755, 4.8632795662984);
    glVertex2f(20.8123151149309, 5.24286021069932);
    glVertex2f(20.8691027703924, 4.9290336936749);
    glVertex2f(20.7704715793276, 4.9768548772215);
    glVertex2f(20.8302480587608, 4.8483354464401);
    glVertex2f(20.6748292122345, 4.8871901580717);
    glVertex2f(20.7166727478377, 4.8094807348085);
    glVertex2f(20.6, 4.8);
    glVertex2f(20.6479297964895, 4.734760135517);
    glEnd();

    //sandcastle
    glBegin(GL_POLYGON);
    glColor3ub(255,197,72);
    glVertex2f(17.1878118886283, 0.2975378847855);
    glVertex2f(18.6338819705209, 0.2975378847855);
    glVertex2f(18.6706313157865, 0.5690556276027);
    glVertex2f(18.5504955229359, 1.1503578510734);
    glVertex2f(17.3026334165519, 1.1503578510734);
    glVertex2f(17.1863729718578, 0.5496788868204);
    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(17.1878118886283, 0.2975378847855);
    glVertex2f(18.6338819705209, 0.2975378847855);
    glVertex2f(18.6706313157865, 0.5690556276027);
    glVertex2f(18.5504955229359, 1.1503578510734);
    glVertex2f(17.3026334165519, 1.1503578510734);
    glVertex2f(17.1863729718578, 0.5496788868204);
    glEnd();
    glBegin(GL_POLYGON);
    glColor3ub(249,255,174);
    glVertex2f(17.1878118886283, 0.2975378847855);
    glVertex2f(18.6338819705209, 0.2975378847855);
    glVertex2f(18.5147301285672, 0.8824651089218);
    glVertex2f(17.3123797233981, 0.8716331232896);
    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(17.1878118886283, 0.2975378847855);
    glVertex2f(18.6338819705209, 0.2975378847855);
    glVertex2f(18.5147301285672, 0.8824651089218);
    glVertex2f(17.3123797233981, 0.8716331232896);
    glEnd();
    glBegin(GL_POLYGON);
    glColor3ub(255,197,72);
    glVertex2f(17.7325768884167, 0.2993324531081);
    glVertex2f(18.050977586164, 0.3027713327995);
    glVertex2f(18.0034637240361, 0.7222961087243);
    glVertex2f(17.7610912921661, 0.7175437080994);
    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(17.7325768884167, 0.2993324531081);
    glVertex2f(18.050977586164, 0.3027713327995);
    glVertex2f(18.0034637240361, 0.7222961087243);
    glVertex2f(17.7610912921661, 0.7175437080994);
    glEnd();

    glBegin(GL_POLYGON);
    glColor3ub(255,253,118);
    glVertex2f(17.8964978684027, 0.9970407601641);
    glVertex2f(18.4404547680141, 0.9863047687244);
    glVertex2f(18.4082467936951, 1.9060213687251);
    glVertex2f(17.9358631703483, 1.9239146877913);
    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(17.8964978684027, 0.9970407601641);
    glVertex2f(18.4404547680141, 0.9863047687244);
    glVertex2f(18.4082467936951, 1.9060213687251);
    glVertex2f(17.9358631703483, 1.9239146877913);
    glEnd();

    glColor3ub(249,255,174);
    drawFilledEllipse(18.1684763182084, 0.9934620963509,0.27,0.06);
    glColor3ub(0,0,0);
    drawLowerHollowTiltedEllipse(18.1684763182084, 0.9934620963509,0.27,0.06,0,100);
    glColor3ub(249,255,174);
    glBegin(GL_TRIANGLES);
    glVertex2f(17.9358631703483, 1.9239146877913);
    glVertex2f(18.4082467936951, 1.9060213687251);
    glVertex2f(18.1792123096481, 2.3497756815659);
    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(17.9358631703483, 1.9239146877913);
    glVertex2f(18.4082467936951, 1.9060213687251);
    glVertex2f(18.1792123096481, 2.3497756815659);
    glEnd();
    glColor3ub(249,255,174);
    drawFilledEllipse(18.1678449707662, 1.9137494178319,0.24,0.05);
    glColor3ub(0,0,0);
    drawLowerHollowTiltedEllipse(18.1678449707662, 1.9137494178319,0.24,0.05,0,100);



    glBegin(GL_POLYGON);
    glColor3ub(255,253,118);
    glVertex2f(17.3740129516707, 1.0149340792303);
    glVertex2f(17.8320819197646, 1.0041980877906);
    glVertex2f(17.8141886006984, 1.53741899596221);
    glVertex2f(17.4312715726825, 1.5338403321489);
    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(17.3740129516707, 1.0149340792303);
    glVertex2f(17.8320819197646, 1.0041980877906);
    glVertex2f(17.8141886006984, 1.53741899596221);
    glVertex2f(17.4312715726825, 1.5338403321489);
    glEnd();

    glColor3ub(249,255,174);
    drawFilledEllipse(17.6, 1,0.23,0.05);
    glColor3ub(0,0,0);
    drawLowerHollowTiltedEllipse(17.6, 1,0.23,0.05,0,100);

    glColor3ub(249,255,174);
    drawFilledEllipse(17.6209407547838, 1.5374189959622,0.19,0.05);
    glColor3ub(0,0,0);
    //drawLowerHollowTiltedEllipse(17.6209407547838, 1.5374189959622,0.19,0.05,0,100);
    drawHollowEllipse(17.6209407547838, 1.5374189959622,0.19,0.05,100);


    //red shovel

    glBegin(GL_POLYGON);
    glColor3ub(219,40,28);
    glVertex2f(16.6598479700956, 0.8660281916735);
    glVertex2f(16.918607121856, 0.7814025571355);
    glVertex2f(17.0129972526869, 0.9685554027484);
    glVertex2f(16.9576651070274, 1.056435869384);
    glVertex2f(16.8551378959525, 1.0922390224578);
    glVertex2f(16.7574929330241, 1.0775922780185);
    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(16.6598479700956, 0.8660281916735);
    glVertex2f(16.918607121856, 0.7814025571355);
    glVertex2f(17.0129972526869, 0.9685554027484);
    glVertex2f(16.9576651070274, 1.056435869384);
    glVertex2f(16.8551378959525, 1.0922390224578);
    glVertex2f(16.7574929330241, 1.0775922780185);
    glEnd();
    glLineWidth(4.0f);
    glBegin(GL_LINES);
    glColor3ub(219,40,28);
    glVertex2f(17.0911132230297, 1.4226044803657);
    glVertex2f(16.9029797169729, 1.0756390203172);
    glEnd();


    //bucket
    glLineWidth(2.0f);
    glBegin(GL_POLYGON);
    glColor3ub(200,142,38);
    glVertex2f(16.21,0.67);
    glVertex2f(16.95,0.67);
    glVertex2f(16.9751488557897, 0.7888742296975);
    glVertex2f(16.6810430962893, 0.8843631126521);
    glVertex2f(16.5282608835618, 0.8862728903112);
    glVertex2f(16.1978693485387, 0.7850546743793);
    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(16.21,0.67);
    glVertex2f(16.95,0.67);
    glVertex2f(16.9751488557897, 0.7888742296975);
    glVertex2f(16.6810430962893, 0.8843631126521);
    glVertex2f(16.5282608835618, 0.8862728903112);
    glVertex2f(16.1978693485387, 0.7850546743793);
    glEnd();

    glBegin(GL_POLYGON);
    glColor3ub(173,116,116);
    glVertex2f(16.3163615021959, 0.0427028193634);
    glVertex2f(16.8343731703102, 0.0474552199883);
    glVertex2f(16.9579355865577, 0.736553310599);
    glVertex2f(16.2023038871983, 0.7222961087243);
    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(16.3163615021959, 0.0427028193634);
    glVertex2f(16.8343731703102, 0.0474552199883);
    glVertex2f(16.9579355865577, 0.736553310599);
    glVertex2f(16.2023038871983, 0.7222961087243);
    glEnd();



}
//S4_SCF
void S4_sandcastle_flag(){
    //flag

    // Flag pole as a quad
    glColor3ub(0, 0, 0);
    glBegin(GL_QUADS);
    glVertex2f(17.60f, 1.53f);
    glVertex2f(17.64f, 1.53f);
    glVertex2f(17.64f, 2.12f);
    glVertex2f(17.60f, 2.12f);
    glEnd();

    glBegin(GL_QUADS);
    glColor3ub(255, 0, 0);

    float waveFactor1 = 0.04f * sin(2 * PI * waveOffset);
    float waveFactor2 = 0.045f * sin(2 * PI * (waveOffset + 0.3f));
    float waveFactor3 = 0.04f * sin(2 * PI * (waveOffset + 0.6f));
    float waveFactor4 = 0.042f * sin(2 * PI * (waveOffset + 0.9f));


    // Increased the width of the flag by adjusting the x-coordinates
    glVertex2f(18.0032681722018, 2.137382171299 + waveFactor1);
    glVertex2f(17.6160445322678 + waveFactor2, 2.1240014415098);
    glVertex2f(17.6019175931456, 1.8477712420937 + waveFactor3);
    glVertex2f(18.0055485732192, 1.8409300390416 + waveFactor4);

    glEnd();

}
//S4_3L
void three_Leaves(){
    //Leaf1
    glPushMatrix();
     glTranslatef(0.0f, 0.0f, 0.0f);
    // glRotatef(LeftLeafAngle, 0.0f, 0.0f, 1.0f);
glBegin(GL_POLYGON);
glColor3ub(21,71,52);
    glVertex2f(19.8772635553422, 2.7727674976646);
    glVertex2f(19.825507402781, 3.1932862372247);
    glVertex2f(19.7381688953339, 3.1997557562948);
    glVertex2f(19.7844026539455, 3.2667253842175);
    glVertex2f(19.6837674348399, 3.4194133028604);
    glVertex2f(19.5206689762895, 3.3482746134927);
    glVertex2f(19.6473305451638, 3.4662607324441);
    glVertex2f(19.609158565503, 3.5148432520123);
    glVertex2f(19.5067882564129, 3.5165783419968);
    glVertex2f(19.5224040662741, 3.598127571272);
    glVertex2f(19.4460601069526, 3.6692662606398);
    glVertex2f(19.3454248878471, 3.598127571272);
    glVertex2f(19.3436897978625, 3.7386698600229);
    glVertex2f(19.00,3.9);
    glVertex2f(19.0088174308388, 3.7143786002388);
    glVertex2f(19.0868964801449, 3.6744715305935);
    glVertex2f(19.045254320515, 3.5998626612566);
    glVertex2f(19.1424193596514, 3.3812413231997);
    glVertex2f(19.2750441386974, 3.4136477763378);
    glVertex2f(19.1875316992504, 3.3343938936161);
    glVertex2f(19.3679810576466, 3.067190035991);
    glVertex2f(19.4634110067984, 3.0446338661914);
    glVertex2f(19.4946426265208, 2.9422635571013);
    glEnd();
    glLineWidth(1.5);
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
    glVertex2f(19.8772635553422, 2.7727674976646);
    glVertex2f(19.825507402781, 3.1932862372247);
    glVertex2f(19.7381688953339, 3.1997557562948);
    glVertex2f(19.7844026539455, 3.2667253842175);
    glVertex2f(19.6837674348399, 3.4194133028604);
    glVertex2f(19.5206689762895, 3.3482746134927);
    glVertex2f(19.6473305451638, 3.4662607324441);
    glVertex2f(19.609158565503, 3.5148432520123);
    glVertex2f(19.5067882564129, 3.5165783419968);
    glVertex2f(19.5224040662741, 3.598127571272);
    glVertex2f(19.4460601069526, 3.6692662606398);
    glVertex2f(19.3454248878471, 3.598127571272);
    glVertex2f(19.3436897978625, 3.7386698600229);
    glVertex2f(19.00,3.9);
    glVertex2f(19.0088174308388, 3.7143786002388);
    glVertex2f(19.0868964801449, 3.6744715305935);
    glVertex2f(19.045254320515, 3.5998626612566);
    glVertex2f(19.1424193596514, 3.3812413231997);
    glVertex2f(19.2750441386974, 3.4136477763378);
    glVertex2f(19.1875316992504, 3.3343938936161);
    glVertex2f(19.3679810576466, 3.067190035991);
    glVertex2f(19.4634110067984, 3.0446338661914);
    glVertex2f(19.4946426265208, 2.9422635571013);
    glEnd();
glLineWidth(1.5);
drawTiltedUpperHollowEllipse(19.493365139957, 3.2562580268776,0.62,0.04,130,100);
    glPopMatrix();
    //Leaf2

    glBegin(GL_POLYGON);
glColor3ub(107,148,100);
    glVertex2f(19.8772635553422, 2.7727674976646);
    glVertex2f(20.1919980965359, 3.2896706652112);
    glVertex2f(20.104815036604, 3.3735005305303);
    glVertex2f(20.2959471295316, 3.3399685844027);
    glVertex2f(20.3563046325614, 3.5713390126835);
    glVertex2f(20.1685257342465, 3.678641240292);
    glVertex2f(20.3797769948507, 3.6384029049388);
    glVertex2f(20.3965429679145, 3.7289391594834);
    glVertex2f(20.3261258810465, 3.8228286086409);
    glVertex2f(20.4367813032677, 3.8697733332196);
    glVertex2f(20.4501940817188, 3.9904883392791);
    glVertex2f(20.2959471295316, 4.0441394530834);
    glVertex2f(20.4535472763316, 4.1547948753046);
    glVertex2f(20.3428918541103, 4.6275953157045);
    glVertex2f(20.1651725396337, 4.449876001228);
    glVertex2f(20.1783002816226, 4.3565283126194);
    glVertex2f(20.082040167697, 4.3417190643232);
    glVertex2f(20.016986236501, 4.2453763066778);
    glVertex2f(20.040166207312, 4.12368145992);
    glVertex2f(19.9340472419433, 4.0652950252052);
    glVertex2f(20.0510736574186, 3.9748655223379);
    glVertex2f(19.9, 4);
    glVertex2f(19.7638270012519, 3.6042818733327);
    glVertex2f(19.8117014439463, 3.4677510552781);
    glVertex2f(19.72, 3.36);
    glEnd();
    glLineWidth(1.5);
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
    glVertex2f(19.8772635553422, 2.7727674976646);
    glVertex2f(20.1919980965359, 3.2896706652112);
    glVertex2f(20.104815036604, 3.3735005305303);
    glVertex2f(20.2959471295316, 3.3399685844027);
    glVertex2f(20.3563046325614, 3.5713390126835);
    glVertex2f(20.1685257342465, 3.678641240292);
    glVertex2f(20.3797769948507, 3.6384029049388);
    glVertex2f(20.3965429679145, 3.7289391594834);
    glVertex2f(20.3261258810465, 3.8228286086409);
    glVertex2f(20.4367813032677, 3.8697733332196);
    glVertex2f(20.4501940817188, 3.9904883392791);
    glVertex2f(20.2959471295316, 4.0441394530834);
    glVertex2f(20.4535472763316, 4.1547948753046);
    glVertex2f(20.3428918541103, 4.6275953157045);
    glVertex2f(20.1651725396337, 4.449876001228);
    glVertex2f(20.1783002816226, 4.3565283126194);
    glVertex2f(20.082040167697, 4.3417190643232);
    glVertex2f(20.016986236501, 4.2453763066778);
    glVertex2f(20.040166207312, 4.12368145992);
    glVertex2f(19.9340472419433, 4.0652950252052);
    glVertex2f(20.0510736574186, 3.9748655223379);
    glVertex2f(19.9, 4);
    glVertex2f(19.7638270012519, 3.6042818733327);
    glVertex2f(19.8117014439463, 3.4677510552781);
    glVertex2f(19.72, 3.36);
    glEnd();
glLineWidth(1.5);
drawTiltedUpperHollowEllipse(20.07, 3.76,0.67,0.034,73,100);
    //Leaf3
    glPushMatrix();
    glTranslatef(0.0,0.0,0.0);
   // glRotatef(LeftLeafAngle,0.0,0.0,1.0);


    glBegin(GL_POLYGON);
glColor3ub(31,134,70);
    glVertex2f(19.8772635553422, 2.7727674976646);
    glVertex2f(20.3276649238026, 2.9034306362164);
    glVertex2f(20.3276649238026, 2.9881965317617);
    glVertex2f(20.3990150012817, 2.9451315910073);
    glVertex2f(20.5506855410139, 3.0406278567646);
    glVertex2f(20.4817143522996, 3.2179710775231);
    glVertex2f(20.6030499560738, 3.0779975115735);
    glVertex2f(20.6585305690028, 3.1214823163016);
    glVertex2f(20.6570310929777, 3.239940922285);
    glVertex2f(20.7544970346097, 3.2219472099838);
    glVertex2f(20.8129765995889, 3.2924225831638);
    glVertex2f(20.7290059421829, 3.4093817131222);
    glVertex2f(20.9, 3.4);
    glVertex2f(21.0738854279576, 3.7857501954241);
    glVertex2f(20.8639587844425, 3.7602591029973);
    glVertex2f(20.8249724077897, 3.6702905414908);
    glVertex2f(20.7604949387101, 3.7272706304449);
    glVertex2f(20.5085829664921, 3.6133104525368);
    glVertex2f(20.5505682951951, 3.490353418478);
    glVertex2f(20.4635986857388, 3.5848204080597);
    glVertex2f(20.2, 3.4);
    glVertex2f(20.144210292391, 3.2759283468876);
    glVertex2f(20.0302501144828, 3.239940922285);
    //glVertex2f()
    glEnd();
glLineWidth(1.5);
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
    glVertex2f(19.8772635553422, 2.7727674976646);
    glVertex2f(20.3276649238026, 2.9034306362164);
    glVertex2f(20.3276649238026, 2.9881965317617);
    glVertex2f(20.3990150012817, 2.9451315910073);
    glVertex2f(20.5506855410139, 3.0406278567646);
    glVertex2f(20.4817143522996, 3.2179710775231);
    glVertex2f(20.6030499560738, 3.0779975115735);
    glVertex2f(20.6585305690028, 3.1214823163016);
    glVertex2f(20.6570310929777, 3.239940922285);
    glVertex2f(20.7544970346097, 3.2219472099838);
    glVertex2f(20.8129765995889, 3.2924225831638);
    glVertex2f(20.7290059421829, 3.4093817131222);
    glVertex2f(20.9, 3.4);
    glVertex2f(21.0738854279576, 3.7857501954241);
    glVertex2f(20.8639587844425, 3.7602591029973);
    glVertex2f(20.8249724077897, 3.6702905414908);
    glVertex2f(20.7604949387101, 3.7272706304449);
    glVertex2f(20.5085829664921, 3.6133104525368);
    glVertex2f(20.5505682951951, 3.490353418478);
    glVertex2f(20.4635986857388, 3.5848204080597);
    glVertex2f(20.2, 3.4);
    glVertex2f(20.144210292391, 3.2759283468876);
    glVertex2f(20.0302501144828, 3.239940922285);
    glEnd();
drawLowerHollowTiltedEllipse(20.46,3.32,0.53,0.03,40,200);
glPopMatrix();

}
//S4_RB
void RightBallon(){

    glPushMatrix();
    glTranslatef(RightBallonX,0.0f, 0.0f);
    glColor3ub(148,0,0);
drawFilledCircle(25.6,14.06,0.93,200);
    glColor3ub(0,0,0);
    glLineWidth(2.0);
drawHollowCircle(25.6,14.06,0.93,200);
drawLowerHollowTiltedEllipse(25.6,14.06,0.915,0.15,720,200);
//strings
glLineWidth(2.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(25.9,13.9);
    glVertex2f(25.73,12.49);
    glEnd();
glLineWidth(2.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(25.26,13.9);
    glVertex2f(25.47,12.50);
    glEnd();
glLineWidth(2.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(24.93,13.95);
    glVertex2f(25.32,12.49);
    glEnd();
glLineWidth(2.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(24.65,14.1);
    glVertex2f(25.22,12.5);
    glEnd();
glLineWidth(2.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(26.25,13.95);
    glVertex2f(25.90,12.49);
    glEnd();
glLineWidth(2.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(26.50,14.01);
    glVertex2f(26.01,12.49);
    glEnd();
    //body
glBegin(GL_QUADS);
    glColor3ub(109,77,43);
    glVertex2f(25.2,11.9);
    glVertex2f(26.01,11.9);
    glVertex2f(26.01,12.5);
    glVertex2f(25.2,12.5);
    glEnd();
drawFilledEllipse(25.62,11.9,0.38,0.06);
glColor3ub(0,0,0);
    glLineWidth(2.0);
drawLowerHollowTiltedEllipse(25.62,11.9,0.38,0.06,0,200);
glColor3ub(109,77,43);
drawFilledEllipse(25.6,12.5,0.38,0.06);
glColor3ub(0,0,0);
glLineWidth(2.0);
drawHollowEllipse(25.6,12.5,0.38,0.06,200);
glLineWidth(2.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(25.22,11.9);
    glVertex2f(25.22,12.5);
    glEnd();
glLineWidth(2.0f);
    glBegin(GL_LINES);
    glColor3ub(0,0,0);
    glVertex2f(26.01,12.5);
    glVertex2f(26.01,11.9);
    glEnd();
    glPopMatrix();

}
//S4_LB
void LeftBallon(){
glPushMatrix();
    glTranslatef(LeftBallonX,0.0f, 0.0f);
    glColor3ub(115,56,160);
drawFilledCircle(15.2,12.7,0.86,200);
    glColor3ub(0,0,0);
    glLineWidth(2.0);
drawHollowCircle(15.2,12.7,0.86,200);
drawLowerHollowTiltedEllipse(15.2,12.6,0.85,0.21,0,200);
glBegin(GL_LINES);
    glVertex2f(14.9,12.4);
    glVertex2f(15.1,11.2);
    glEnd();
glBegin(GL_LINES);
    glVertex2f(14.6,12.45);
    glVertex2f(15.0,11.2);
    glEnd();
glBegin(GL_LINES);
    glVertex2f(14.34,12.6);
    glVertex2f(14.9,11.2);
    glEnd();
glBegin(GL_LINES);
    glVertex2f(15.3,11.2);
    glVertex2f(15.5,12.4);
    glEnd();
glBegin(GL_LINES);
    glVertex2f(15.8,12.46);
    glVertex2f(15.5,11.2);
    glEnd();
glBegin(GL_LINES);
    glVertex2f(16.06,12.6);
    glVertex2f(15.6,11.2);
    glEnd();
//body
glBegin(GL_QUADS);
    glColor3ub(109,77,43);
    glVertex2f(14.85,10.5);
    glVertex2f(15.65,10.5);
    glVertex2f(15.65,11.2);
    glVertex2f(14.85,11.2);
    glEnd();
drawFilledEllipse(15.25,10.5,0.38,0.07);
glColor3ub(0,0,0);
drawLowerHollowTiltedEllipse(15.25,10.5,0.38,0.07,0,200);
glColor3ub(109,77,43);
drawFilledEllipse(15.25,11.2,0.38,0.07);
glColor3ub(0,0,0);
drawHollowEllipse(15.25,11.2,0.38,0.07,200);
glBegin(GL_LINES);
    glVertex2f(14.85,11.2);
    glVertex2f(14.85,10.5);
    glEnd();
glBegin(GL_LINES);
    glVertex2f(15.65,11.2);
    glVertex2f(15.65,10.5);
    glEnd();

    glPopMatrix();
}
void Stones(){
    //Stone1
glBegin(GL_POLYGON);
    glColor3ub(123,79,68);
    glVertex2f(28.7,0.0);
    glVertex2f(30.0,0.0);
    glVertex2f(30.0,1.0);
    glVertex2f(29.6,1.2);
    glVertex2f(29.3,0.99);
    glVertex2f(29.3,0.66);
    glEnd();
    glLineWidth(2.0);
/*glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(28.7,0.0);
    glVertex2f(30.0,0.0);
    glVertex2f(30.0,1.0);
    glVertex2f(29.6,1.2);
    glVertex2f(29.3,0.99);
    glVertex2f(29.3,0.66);
    glEnd();*/
    //Stone2
    glBegin(GL_POLYGON);
    glColor3ub(123,79,68);
    glVertex2f(29.6,1.2);
    glVertex2f(29.3,1.2);
    glVertex2f(29.1,1.3);
    glVertex2f(28.4654195020674, 1.0733558197022);
    glVertex2f(27.7981680049524, 0.2967430523521);
    glVertex2f(28.1985784777704, 0.0872975742627);
    glVertex2f(28.7098718507534, 0.0235853259022);
    glVertex2f(29.3092963170291, 0.9962416950204);
    glVertex2f(29.1,1.3);
    glEnd();
    glBegin(GL_POLYGON);
    glColor3ub(62,39,35);
    glVertex2f(27.7981680049524, 0.2967430523521);
    glVertex2f(28.1985784777704, 0.0872975742627);
    glVertex2f(28.6428328880332, 0.9001054380701);
    glVertex2f(29.6123001680084, 1.250764929843);
    glVertex2f(29.3521346120808, 1.2237118600958);
    glVertex2f(29.1169623437729, 1.3085280880101);
    glVertex2f(28.4654195020674, 1.0733558197022);
    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(29.6,1.2);
    glVertex2f(29.3,1.2);
    glVertex2f(29.1,1.3);
    glVertex2f(28.4654195020674, 1.0733558197022);
    glVertex2f(27.7981680049524, 0.2967430523521);
    glVertex2f(28.1985784777704, 0.0872975742627);
    glVertex2f(28.7098718507534, 0.0235853259022);
    glVertex2f(29.3092963170291, 0.9962416950204);
    glVertex2f(29.1,1.3);
    glEnd();
    //Stone3
    glBegin(GL_POLYGON);
    glColor3ub(123,79,68);
    glVertex2f(27.4901599489385, 0.6601925584484);
    glVertex2f(27.5825623657427, 0.4630674025996);
    glVertex2f(27.7981680049524, 0.2967430523521);
    glVertex2f(28.4654195020674, 1.0733558197022);
    glVertex2f(28.2416996056123, 1.1899664147922);
    glVertex2f(27.9028907439971, 1.0852436757475);
    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(27.4901599489385, 0.6601925584484);
    glVertex2f(27.5825623657427, 0.4630674025996);
    glVertex2f(27.7981680049524, 0.2967430523521);
    glVertex2f(28.4654195020674, 1.0733558197022);
    glVertex2f(28.2416996056123, 1.1899664147922);
    glVertex2f(27.9028907439971, 1.0852436757475);
    glEnd();
    //Stone4
    glBegin(GL_POLYGON);
    glColor3ub(62,39,35);
    glVertex2f(25.7374752257486, 0.7204284547293);
    glVertex2f(27.0629417379546, 0.4343145379976);
    glVertex2f(27.5825623657427, 0.4630674025996);
    glVertex2f(27.4901599489385, 0.6601925584484);
    glVertex2f(27.4366415475633, 0.8839221214331);
    glVertex2f(26.4673576663907, 1.5904483239746);
    glVertex2f(26.5315873211672, 1.4094374786954);
    glVertex2f(26.0177500829552, 1.1291626214888);
    glVertex2f(27.5825623657427, 0.4630674025996);
    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(25.7374752257486, 0.7204284547293);
    glVertex2f(27.0629417379546, 0.4343145379976);
    glVertex2f(27.5825623657427, 0.4630674025996);
    glVertex2f(27.4901599489385, 0.6601925584484);
    glVertex2f(27.4366415475633, 0.8839221214331);
    glVertex2f(26.4673576663907, 1.5904483239746);
    glVertex2f(26.5315873211672, 1.4094374786954);
    glVertex2f(26.0177500829552, 1.1291626214888);
    glVertex2f(27.5825623657427, 0.4630674025996);
    glEnd();
    //Stone5
    glBegin(GL_POLYGON);
    glColor3ub(123,79,68);
    glVertex2f(24.2717032220415, 1.405672267292);
    glVertex2f(24.8072660737938, 0.9724079377846);
    glVertex2f(25.7374752257486, 0.7204284547293);
    glVertex2f(26.0177500829552, 1.1291626214888);
    glVertex2f(26.5315873211672, 1.4094374786954);
    glVertex2f(26.4673576663907, 1.5904483239746);
    glVertex2f(26.3056385466738, 1.6764624732341);
    glVertex2f(25.6798122929408, 1.532041030065);
    glVertex2f(25.2826533242256, 1.6824800333662);
    glVertex2f(24.9938104378873, 1.4237249476881);
    glVertex2f(24.9035470359066, 1.6704449131021);
    glVertex2f(24.4763002665312, 1.6283219921778);
    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(24.2717032220415, 1.405672267292);
    glVertex2f(24.8072660737938, 0.9724079377846);
    glVertex2f(25.7374752257486, 0.7204284547293);
    glVertex2f(26.0177500829552, 1.1291626214888);
    glVertex2f(26.5315873211672, 1.4094374786954);
    glVertex2f(26.4673576663907, 1.5904483239746);
    glVertex2f(26.3056385466738, 1.6764624732341);
    glVertex2f(25.6798122929408, 1.532041030065);
    glVertex2f(25.2826533242256, 1.6824800333662);
    glVertex2f(24.9938104378873, 1.4237249476881);
    glVertex2f(24.9035470359066, 1.6704449131021);
    glVertex2f(24.4763002665312, 1.6283219921778);
    glEnd();
    //Stone6
    glBegin(GL_POLYGON);
    glColor3ub(123,79,68);
    glVertex2f(22.8888679036969, 1.6734536931681);
    glVertex2f(23, 1.4);
    glVertex2f(23.5626744243839, 1.10702935074);
    glVertex2f(24.0961198318958, 1.3247621701325);
    glVertex2f(24.4763002665312, 1.6283219921778);
    glVertex2f(24.0671061775519, 1.7859820676374);
    glEnd();
    glBegin(GL_POLYGON);
    glColor3ub(62,39,35);
    glVertex2f(22.8888679036969, 1.6734536931681);
    glVertex2f(23, 1.4);
    glVertex2f(23.3304260836985, 1.2449268030219);
    glVertex2f(24.0671061775519, 1.7859820676374);
    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(22.8888679036969, 1.6734536931681);
    glVertex2f(23, 1.4);
    glVertex2f(23.5626744243839, 1.10702935074);
    glVertex2f(24.0961198318958, 1.3247621701325);
    glVertex2f(24.4763002665312, 1.6283219921778);
    glVertex2f(24.0671061775519, 1.7859820676374);
    glEnd();
    //Stone7
    glBegin(GL_POLYGON);
    glColor3ub(123,79,68);
    glVertex2f(22.8888679036969, 1.6734536931681);
    glVertex2f(24.0671061775519, 1.7859820676374);
    glVertex2f(23.5882908756111, 2.7599952300084);
    glVertex2f(22.6413160145788, 2.3833575011887);
    glEnd();
    glBegin(GL_POLYGON);
    glColor3ub(62,39,35);
    glVertex2f(22.8888679036969, 1.6734536931681);
    glVertex2f(23.0400572812497, 2.1862852006721);
    glVertex2f(23.5882908756111, 2.7599952300084);
    glVertex2f(22.6413160145788, 2.3833575011887);
    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(22.8888679036969, 1.6734536931681);
    glVertex2f(24.0671061775519, 1.7859820676374);
    glVertex2f(23.5882908756111, 2.7599952300084);
    glVertex2f(22.6413160145788, 2.3833575011887);
    glEnd();
    //Stone8
    glBegin(GL_POLYGON);
    glColor3ub(62,39,35);
    glVertex2f(27.104200522918, 1.8292441759069);
    glVertex2f(27.8705126881371, 1.5629831693477);
    glVertex2f(27.5912633397946, 3.6735887091462);
    glVertex2f(27.3250023332354, 3.4917519241789);
    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(27.104200522918, 1.8292441759069);
    glVertex2f(27.8705126881371, 1.5629831693477);
    glVertex2f(27.5912633397946, 3.6735887091462);
    glVertex2f(27.3250023332354, 3.4917519241789);
    glEnd();
    glBegin(GL_POLYGON);
    glColor3ub(123,79,68);
    glVertex2f(27.8705126881371, 1.5629831693477);
    glVertex2f(28.4160230430389, 1.7578082960984);
    glVertex2f(27.5912633397946, 3.6735887091462);
    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3ub(0,0,0);
    glVertex2f(27.8705126881371, 1.5629831693477);
    glVertex2f(28.4160230430389, 1.7578082960984);
    glVertex2f(27.5912633397946, 3.6735887091462);
    glEnd();

}
void BeachBall(){
glPushMatrix();
//glTranslatef(BallPositionX,3.0f,0.0f);
//glRotatef(BallAngle,0.0f,0.0f,1.0f);
glColor3ub(218,248,227);
//drawFilledCircle(0.0,0.0,ballRadius,200);
glColor3ub(0,0,0);
//drawHollowCircle(0.0,0.0,ballRadius,200);
glColor3ub(0,0,0);
drawFilledCircle(0.13,0.18,0.05,200);
drawHollowCircle(0.13,0.18,0.05,200);

//semi-circle stripes
drawTiltedHollowSemiCircle(0.29,0.5,0.4,45,true);
drawTiltedHollowSemiCircle(0.55,0.63,0.7,80,true);
glPopMatrix();
}
//S4_BBQ
void bbqfire(){
    float waveAmplitude = 0.05f; // How high the flame moves
    float waveSpeed = 4.0f; // How fast the wave moves

    //flameoutside
    glBegin(GL_POLYGON);
    glColor4ub(255, 154, 0, 223);
    glVertex2f(11.0718, 0.4866);
    glVertex2f(11.1401, 0.5993 + waveAmplitude * sin(waveSpeed * waveOffset));
    glVertex2f(11.2113, 0.7119 + waveAmplitude * sin(waveSpeed * waveOffset + PI / 4));
    glVertex2f(11.2565, 0.8409 + waveAmplitude * sin(waveSpeed * waveOffset + PI / 2));
    glVertex2f(11.2757, 0.9802 + waveAmplitude * sin(waveSpeed * waveOffset + 3 * PI / 4));
    glVertex2f(11.2594, 1.1320 + waveAmplitude * sin(waveSpeed * waveOffset + PI));
    glVertex2f(11.1791, 1.2806 + waveAmplitude * sin(waveSpeed * waveOffset + 5 * PI / 4));
    glVertex2f(11.0411, 1.4348 + waveAmplitude * sin(waveSpeed * waveOffset + 3 * PI / 2));
    glVertex2f(10.9276, 1.5454 + waveAmplitude * sin(waveSpeed * waveOffset + 7 * PI / 4));
    glVertex2f(10.8143, 1.6025);
    glVertex2f(10.7819, 1.5221 + waveAmplitude * sin(waveSpeed * waveOffset + PI));
    glVertex2f(10.7383, 1.3998 + waveAmplitude * sin(waveSpeed * waveOffset + 5 * PI / 4));
    glVertex2f(10.6568, 1.2339);
    glVertex2f(10.5665, 1.0680 + waveAmplitude * sin(waveSpeed * waveOffset + 3 * PI / 4));
    glVertex2f(10.4792, 0.8962 + waveAmplitude * sin(waveSpeed * waveOffset + PI / 4));
    glVertex2f(10.4734, 0.7390 + waveAmplitude * sin(waveSpeed * waveOffset));
    glVertex2f(10.5549, 0.5760);
    glVertex2f(10.6830, 0.4886);
    glEnd();

// Inner flame (Red)
    glBegin(GL_POLYGON);
    glColor4ub(255, 0, 0, 223);
    glVertex2f(10.8914, 1.4219 + waveAmplitude * sin(waveSpeed * waveOffset));
    glVertex2f(10.8986, 1.2329 + waveAmplitude * sin(waveSpeed * waveOffset + PI / 8));
    glVertex2f(10.8805, 1.1512 + waveAmplitude * sin(waveSpeed * waveOffset + PI / 4));
    glVertex2f(10.8569, 1.0714 + waveAmplitude * sin(waveSpeed * waveOffset + PI / 4));
    glVertex2f(10.8079, 0.9607 + waveAmplitude * sin(waveSpeed * waveOffset + PI / 2));
    glVertex2f(10.7662, 0.8628 + waveAmplitude * sin(waveSpeed * waveOffset + 3 * PI / 4));
    glVertex2f(10.7426, 0.7612 + waveAmplitude * sin(waveSpeed * waveOffset + PI));
    glVertex2f(10.7407, 0.6759 + waveAmplitude * sin(waveSpeed * waveOffset + 5 * PI / 4));
    glVertex2f(10.7662, 0.6070 + waveAmplitude * sin(waveSpeed * waveOffset + 3 * PI / 2));
    glVertex2f(10.8115, 0.5507);
    glVertex2f(10.8841, 0.4945);
    glVertex2f(10.9657, 0.4963);
    glVertex2f(11.0129, 0.5543);
    glVertex2f(11.0619, 0.6287 + waveAmplitude * sin(waveSpeed * waveOffset + PI / 6));
    glVertex2f(11.1, 0.7 + waveAmplitude * sin(waveSpeed * waveOffset + PI / 3));
    glVertex2f(11.1181, 0.7956 + waveAmplitude * sin(waveSpeed * waveOffset + PI / 2));
    glVertex2f(11.1235, 0.9045 + waveAmplitude * sin(waveSpeed * waveOffset + 3 * PI / 4));
    glVertex2f(11.1138, 0.9999 + waveAmplitude * sin(waveSpeed * waveOffset + PI));
    glVertex2f(11.0836, 1.0823 + waveAmplitude * sin(waveSpeed * waveOffset + 5 * PI / 4));
    glVertex2f(11.0365, 1.1658 + waveAmplitude * sin(waveSpeed * waveOffset + 3 * PI / 2));
    glVertex2f(10.9849, 1.2806 + waveAmplitude * sin(waveSpeed * waveOffset + 7 * PI / 4));
    glVertex2f(10.8986, 1.2329 + waveAmplitude * sin(waveSpeed * waveOffset));
    glEnd();
}
void firelogs(){

    glColor3ub(87, 67, 50);
    glBegin(GL_QUADS);
    glVertex2f(10.5557510825195, 0.2647292527224);
    glVertex2f(10.9545092145784, 0.5191509316255);
    glVertex2f(10.8797120920338, 0.5339362887976);
    glVertex2f(10.3245676391102, 0.3301298321079);

    glVertex2f(10.5572720262261, 0.1430537561912);
    glVertex2f(10.5557510825195, 0.2647292527224);
    glVertex2f(10.3245676391102, 0.3301298321079);
    glVertex2f(10.3245676391102, 0.2616873653091);

    glVertex2f(10.9542383336592, 0.4274702293328);
    glVertex2f(10.9545092145784, 0.5191509316255);
    glVertex2f(10.5557510825195, 0.2647292527224);
    glVertex2f(10.5572720262261, 0.1430537561912);

    glVertex2f(11.6038333907652, 0.2692920838423);
    glVertex2f(10.9572802210725, 0.5430619510374);
    glVertex2f(10.8797120920338, 0.5339362887976);
    glVertex2f(11.2601001130645, 0.1810773488572);


    glVertex2f(11.6083962218851, 0.1515694019791);
    glVertex2f(11.6038333907652, 0.2692920838423);
    glVertex2f(11.2601001130645, 0.1810773488572);
    glVertex2f(11.2646629441845, 0.0244201470733);


    glVertex2f(11.2631420004778, 0.0183363722467);
    glVertex2f(11.2601001130645, 0.1810773488572);
    glVertex2f(10.8797120920338, 0.5339362887976);
    glVertex2f(10.8829060738178, 0.4274702293328);


    glVertex2f(11.67379680127064, 0.5430619510374379);
    glVertex2f(11.1543537087874, 0.5782064687594);
    glVertex2f(11.075790058054, 0.5529885067956);
    glVertex2f(11.50649299354023, 0.47614042794529166);


    glVertex2f(11.6796904590113, 0.4131055506072);
    glVertex2f(11.67379680127064, 0.5430619510374379);
    glVertex2f(11.4975490793098, 0.4794935301246);
    glVertex2f(11.4992513351949, 0.3824649446762);


    glVertex2f(11.4992513351949, 0.3824649446762);
    glVertex2f(11.4975490793098, 0.4794935301246);
    glVertex2f(11.075790058054, 0.5529885067956);
    glVertex2f(11.0377847584808, 0.475233632949);





    glEnd();


}
//S4_T
void thunder(){

   if (!thunderVisible) return; // Only render if visible

    glColor3f(1.0f, 1.0f, 0.5f); //  color for thunder
    glLineWidth(5.0f);
    glBegin(GL_LINES);

    glVertex2f(4.3408236583775, 11.8490970160038);
    glVertex2f(3.3944630361272, 10.779662469963);

    glVertex2f(3.3944630361272, 10.779662469963);
    glVertex2f(4.1575729253572, 11.0489953720441);

    glVertex2f(4.1575729253572, 11.0489953720441);
    glVertex2f(3.2298707070776, 9.477886776571);

    glVertex2f(4.8395275433077, 11.8662937016911);
    glVertex2f(5.1152010216458, 11.3632170911387);

    glVertex2f(5.1152010216458, 11.3632170911387);
    glVertex2f(5.5042374357631, 11.8869199562965);

    glVertex2f(5.5042374357631, 11.8869199562965);
    glVertex2f(6.6264578611013, 9.50781265458);

    glVertex2f(6.6264578611013, 9.50781265458);
    glVertex2f(6.8957907631825, 10.1661819707783);

    glVertex2f(6.8957907631825, 10.1661819707783);
    glVertex2f(8.8708987117778, 5.7820408424581);


    glEnd();


}

//S4_keyb
void ab_keyboard(unsigned char key, int x, int y) {
 if (key == 't' || key == 'T') {
        thunderVisible = true;
        lastToggleTime = clock();
    }
}



//S4_u_F
void flame_update(int value) {
    waveOffset += 0.1f;

}
//Update functions
//S4_URBP
UpdateRightBallonPos(){
 RightBallonX -= RightBallonSpeed;
    if (RightBallonX >4 || RightBallonX <-4) {
        RightBallonSpeed = -RightBallonSpeed;
    }
}
//S4_ULBP
UpdateLeftBallonPos(){
LeftBallonX+=LeftBallonSpeed;
if(LeftBallonX>2 || LeftBallonX<-2){
    LeftBallonSpeed= - LeftBallonSpeed;
}
}

//S4_UJS
void UpdateJuiceSign(int value){
    JuiceSignelapsedTime+=value;
    if (JuiceSignelapsedTime>=1000){
        JuiceSignVisible = !JuiceSignVisible;
        JuiceSignelapsedTime = 0;
    }
}
//S4_US
void updateS4_Stars(int value) {
    static int elapsedTime = 0;
    elapsedTime += value;

    if (elapsedTime >= 5000) {
        for (int i = 0; i < numS4_Stars; i++) {
            starVisible[i] = rand() % 8;
        }
        elapsedTime = 0;
    }
    for (int i = 0; i < numS4_Stars; i++) {
        if (starVisible[i]) {
            starBrightness[i] += randomFloat(-0.05, 0.05);
            if (starBrightness[i] > 1.0) starBrightness[i] = 1.0;
            if (starBrightness[i] < 0.5) starBrightness[i] = 0.5;
        }
    }
    //glutPostRedisplay();
    //glutTimerFunc(90, updateS4_Stars, 0);
}
//S4_UW1
void UpdateWave1(){
Wave1X-=Wave1Speed;
if (Wave1X>0.4 || Wave1X<-0.4){
    Wave1Speed= - Wave1Speed;
}
}
//S4_UW2
void UpdateWave2(){
Wave2X-=Wave2Speed;
if (Wave2X>0.4 || Wave2X<-0.4){
    Wave2Speed= - Wave2Speed;
}
}
//S4_UW3
void UpdateWave3(){
Wave3X-=Wave3Speed;
if (Wave2X>0.4 || Wave2X<-0.4){
    Wave3Speed= - Wave3Speed;
}
}
//S4_ULB
void S4_UpdateLightBeam(int value) {
    if (S4_beamActive) {
        S4_beamAngle += S4_BeamSpeed;
        if (S4_beamAngle >= 270.0f) {
            S4_beamAngle -= 270.0f;
        }
    }
}
//S4_USCF
void S4_sandcastle_flag_update(int value) {
    waveOffset += 0.1f; // Slower wave motion
}

// Timer function to turn off thunder after some time
//S4_TH
void timer(int value) {
    if (thunderVisible && (clock() - lastToggleTime) > thunderDuration) {
        thunderVisible = false;

    }
}
//S4_mouse
void ab_mouse(int button, int state, int x, int y) {
    if (state == GLUT_DOWN) {
        if (button == GLUT_LEFT_BUTTON) {
            S4_beamActive = !S4_beamActive; // Toggle beam animation
            if (S4_beamActive) {
                glutTimerFunc(16, S4_UpdateLightBeam, 0); // Restart animation
            }
        }
        else if (button == GLUT_RIGHT_BUTTON) {
            S4_beamActive = false; // Stop the beam completely
            S4_beamAngle = 0.0f;   // Reset the angle
            glutPostRedisplay();   // Force redraw
        }
    }
}



void display() {
     //glClearColor(0.937f, 0.847f, 0.710f, 1.0f);
    glClearColor(0.1569f, 0.1412f, 0.2588f, 1.0f);
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
    //S4_BusStand();
        // Draw all active circles


    S4_SoilAndDetails();
    Crab();
    S4_SurfBoard();
    SoilStrip();
    bbqfire();
    firelogs();
    S4_BBQ_place();
    S4_SkyAndClouds();
    S4_LightHouseAndCliff();
    S4_ChairAndUmbrellaAndCoconutTreeAndSandcastle ();
    S4_Stars();
    Cloud1();
    //S4_LightHouseAndCliff();
    S4_LightHouseBeam();
    S4_SeaWater();
    Wave1();
    Wave2();
    Wave3();
    S4_BeachJuiceShop();
    if(JuiceSignVisible==true){
        S4_BoardLighted();
    }else{
     S4_BoardNotLighted();
     }
    three_Leaves();
    RightBallon();
    LeftBallon();
    Stones();
    //BeachBall();
    S4_BusStand();
    S4_sandcastle_flag();
    thunder();


     glFlush();




}







///////////////////////////////////////////////////////////
//arman a r m a n






float opacity = 100;

void path(){
    glBegin(GL_POLYGON);
    glColor3f(0.953, 0.973, 0.796); // Light yellow

    // Adjusting y-coordinates by -0.27 to move the path down
    glVertex2f(0, 3);
    glVertex2f(2.9250602877645, 2.8330532768222);
    glVertex2f(4.8119528655895, 3.0656838686088);
    glVertex2f(7.3191936881789, 3.039836025077);
    glVertex2f(7.3191936881789, 3.039836025077);
    glVertex2f(12.049, 2.911 - 0.27);
    glVertex2f(14.686, 2.885 - 0.27);
    glVertex2f(16.547, 3.092 - 0.27);
    glVertex2f(18.4932652432189, 2.7162342411408 - 0.27);
    glVertex2f(21.1278714208712, 2.6179738708787 - 0.27);
    glVertex2f(23.3244942806265, 2.6637754859671 - 0.27);
    glVertex2f(25.4497432639327, 2.8272561769907 - 0.27);
    glVertex2f(26.705, 3.428 - 0.27);
    glVertex2f(28.230, 3.143 - 0.27);
    glVertex2f(29.961, 3.416 - 0.27);
    glVertex2f(30, 2 - 0.27);
    glVertex2f(27.44, 1.865 - 0.27);
    glVertex2f(25.244, 2.054 - 0.27);
    glVertex2f(23.143, 1.818 - 0.27);
    glVertex2f(20.594, 1.983 - 0.27);
    glVertex2f(18, 2 - 0.27);
    glVertex2f(15.070, 1.865 - 0.27);
    glVertex2f(12, 2 - 0.27);
    glVertex2f(9.428, 1.983 - 0.27);
    glVertex2f(6.407, 2.054 - 0.27);
    glVertex2f(3.102, 1.865 - 0.27);
    glVertex2f(0, 2 - 0.27);

    glEnd();
}
void path_down_ground()
{
    glBegin(GL_POLYGON);
   glColor3f(0.274, 0.310, 0.361);


    glVertex2f(0,0);
    glVertex2f(30,0);
    glVertex2f(30, 2);
    glVertex2f(0,2);
    glEnd();

    //middle line
       glBegin(GL_POLYGON);
   glColor3f(1,1,1);
    glVertex2f(0.8, 0.9);
    glVertex2f(2.4, 0.9);
    glVertex2f(2.4, 1.1);
    glVertex2f(0.8, 1.1);
    glEnd();


      // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(0.8, 0.9);
    glVertex2f(2.4, 0.9);
    glVertex2f(2.4, 1.1);
    glVertex2f(0.8, 1.1);
    glEnd();


      //middle line 2
       glBegin(GL_POLYGON);
   glColor3f(1,1,1);
    glVertex2f(4.2, 1.1);
    glVertex2f(5.7, 1.1);
    glVertex2f(5.7, 0.9);
    glVertex2f(4.2, 0.9);
    glEnd();


      // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
     glVertex2f(4.2, 1.1);
    glVertex2f(5.7, 1.1);
    glVertex2f(5.7, 0.9);
    glVertex2f(4.2, 0.9);
    glEnd();




        //middle line 3
       glBegin(GL_POLYGON);
   glColor3f(1,1,1);
    glVertex2f(8.2, 1.1);
    glVertex2f(9.5, 1.1);
    glVertex2f(9.5, 0.9);
    glVertex2f(8.2, 0.9);
    glEnd();


      // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
     glVertex2f(8.2, 1.1);
    glVertex2f(9.5, 1.1);
    glVertex2f(9.5, 0.9);
    glVertex2f(8.2, 0.9);
    glEnd();




           //middle line 4
       glBegin(GL_POLYGON);
   glColor3f(1,1,1);
    glVertex2f(12.4, 1.1);
    glVertex2f(14, 1.1);
    glVertex2f(14, 0.9);
    glVertex2f(12.4, 0.9);
    glEnd();


      // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(12.4, 1.1);
    glVertex2f(14, 1.1);
    glVertex2f(14, 0.9);
    glVertex2f(12.4, 0.9);
    glEnd();




            //middle line 5
       glBegin(GL_POLYGON);
   glColor3f(1,1,1);
    glVertex2f(16.8, 1);
    glVertex2f(18.6, 1);
    glVertex2f(18.6, 0.8);
    glVertex2f(16.8, 0.8);
    glEnd();


      // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
  glVertex2f(16.8, 1);
    glVertex2f(18.6, 1);
    glVertex2f(18.6, 0.8);
    glVertex2f(16.8, 0.8);
    glEnd();




                //middle line 6
       glBegin(GL_POLYGON);
   glColor3f(1,1,1);
    glVertex2f(21, 1);
    glVertex2f(23.2, 1);
    glVertex2f(23.2, 0.8);
    glVertex2f(21, 0.8);
    glEnd();


      // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
  glVertex2f(21, 1);
    glVertex2f(23.2, 1);
    glVertex2f(23.2, 0.8);
    glVertex2f(21, 0.8);
    glEnd();




                //middle line 7
       glBegin(GL_POLYGON);
   glColor3f(1,1,1);
    glVertex2f(26.4, 1);
    glVertex2f(28.6, 1);
    glVertex2f(28.6, 0.8);
    glVertex2f(26.4, 0.8);
    glEnd();


      // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(26.4, 1);
    glVertex2f(28.6, 1);
    glVertex2f(28.6, 0.8);
    glVertex2f(26.4, 0.8);
    glEnd();

}
void house(){

    glLineWidth(1.0f);
    // roof
    glBegin(GL_POLYGON);
    glColor3f(0.992f, 0.224f, 0.231f); // Steel blue
    glVertex2f(19.9954352043112, 6.7002585126804);
    glVertex2f(22.7200571286011, 6.7076363257484);
    glVertex2f(21.5, 5.5);
    glVertex2f(19, 5.5);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(19.9954352043112, 6.7002585126804);
    glVertex2f(22.7200571286011, 6.7076363257484);
    glVertex2f(21.5, 5.5);
    glVertex2f(19, 5.5);
    glEnd();

    // chimni front
    glBegin(GL_POLYGON);
    glColor3f(0.0, 0.5, 0.7); // Steel blue
    glVertex2f(20.4, 7.2);
    glVertex2f(21, 7.2);
    glVertex2f(21, 6);
    glVertex2f(20.4, 6);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(20.4, 7.2);
    glVertex2f(21, 7.2);
    glVertex2f(21, 6);
    glVertex2f(20.4, 6);
    glEnd();

    // chimni back
    glBegin(GL_POLYGON);
    glColor3f(0.647f, 0.812f, 0.855f); // Steel blue
    glVertex2f(21, 7.2);
    glVertex2f(21.2, 7.2);
    glVertex2f(21.2, 6.4);
    glVertex2f(21, 6);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(21, 7.2);
    glVertex2f(21.2, 7.2);
    glVertex2f(21.2, 6.4);
    glVertex2f(21, 6);
    glEnd();

    // chimni top front
    glBegin(GL_POLYGON);
    glColor3f(0.969f, 0.965f, 0.831f);

    glVertex2f(20.4, 7.4);
    glVertex2f(21, 7.4);
    glVertex2f(21, 7.2);
    glVertex2f(20.4, 7.2);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(20.4, 7.4);
    glVertex2f(21, 7.4);
    glVertex2f(21, 7.2);
    glVertex2f(20.4, 7.2);
    glEnd();

    // chimni top back
    glBegin(GL_POLYGON);
    glColor3f(0.169f, 0.529f, 0.635f);

    glVertex2f(21, 7.4);
    glVertex2f(21.2, 7.4);
    glVertex2f(21.2, 7.2);
    glVertex2f(21, 7.2);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(21, 7.4);
    glVertex2f(21.2, 7.4);
    glVertex2f(21.2, 7.2);
    glVertex2f(21, 7.2);
    glEnd();

    // house front
    glBegin(GL_POLYGON);
    glColor3f(0.267f, 0.592f, 0.749f);

    glVertex2f(21.5, 5.5);
    glVertex2f(22.7200571286011, 6.7076363257484);
    glVertex2f(24, 5.5);
    glVertex2f(24.001, 3.12);
    glVertex2f(21.5, 3.1);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(21.5, 5.5);
    glVertex2f(22.7200571286011, 6.7076363257484);
    glVertex2f(24, 5.5);
    glVertex2f(24.001, 3.12);
    glVertex2f(21.5, 3.1);
    glEnd();

    // house front top corner
    glBegin(GL_POLYGON);
    glColor3f(0.161f, 0.157f, 0.404f);

    glVertex2f(22.7200571286011, 6.7076363257484);
    glVertex2f(24, 5.5);
    glVertex2f(24, 5.2);
    glVertex2f(22.6, 6.6);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(22.7200571286011, 6.7076363257484);
    glVertex2f(24, 5.5);
    glVertex2f(24, 5.2);
    glVertex2f(22.6, 6.6);
    glEnd();
    // house front downpart
    glBegin(GL_POLYGON);
    glColor3f(0.161f, 0.157f, 0.404f);
    glVertex2f(21.4865306841878, 3.3985742055293);
    glVertex2f(24, 3.4);
    glVertex2f(24.001, 3.12);
    glVertex2f(21.5, 3.1);

    glEnd();

    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(21.4865306841878, 3.3985742055293);
    glVertex2f(24, 3.4);
    glVertex2f(24.001, 3.12);
    glVertex2f(21.5, 3.1);
    glEnd();

    // house front top window
    glBegin(GL_POLYGON);
    glColor3f(0.576f, 0.776f, 0.839f);

    glVertex2f(22.6, 6);
    glVertex2f(23, 6);
    glVertex2f(23, 5.6);
    glVertex2f(22.6, 5.6);

    glEnd();

    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color

    glVertex2f(22.6, 6);
    glVertex2f(23, 6);
    glVertex2f(23, 5.6);
    glVertex2f(22.6, 5.6);

    glEnd();

    // house front down 2nd window 1st block with black border
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f); // Window color
    glVertex2f(23, 4.8);
    glVertex2f(23.2, 4.8);
    glVertex2f(23.2, 4.3);
    glVertex2f(23, 4.3);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(23, 4.8);
    glVertex2f(23.2, 4.8);
    glVertex2f(23.2, 4.3);
    glVertex2f(23, 4.3);
    glEnd();

    // house front down 2nd window 2nd block with black border
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f); // Window color
    glVertex2f(23.2, 4.8);
    glVertex2f(23.4, 4.8);
    glVertex2f(23.4, 4.3);
    glVertex2f(23.2, 4.3);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(23.2, 4.8);
    glVertex2f(23.4, 4.8);
    glVertex2f(23.4, 4.3);
    glVertex2f(23.2, 4.3);
    glEnd();

    // house front down 2nd window 3rd block with black border
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f); // Window color
    glVertex2f(23.2, 4.3);
    glVertex2f(23.4, 4.3);
    glVertex2f(23.4, 3.8);
    glVertex2f(23.2, 3.8);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(23.2, 4.3);
    glVertex2f(23.4, 4.3);
    glVertex2f(23.4, 3.8);
    glVertex2f(23.2, 3.8);
    glEnd();

    // house front down 2nd window 4th block with black border
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f); // Window color
    glVertex2f(23, 4.3);
    glVertex2f(23.2, 4.3);
    glVertex2f(23.2, 3.8);
    glVertex2f(23, 3.8);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(23, 4.3);
    glVertex2f(23.2, 4.3);
    glVertex2f(23.2, 3.8);
    glVertex2f(23, 3.8);
    glEnd();

    // house front down 1st window 1st h block with black border
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f); // Window color
    glVertex2f(22, 4.8);
    glVertex2f(22.2, 4.8);
    glVertex2f(22.2, 4.3);
    glVertex2f(22, 4.3);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(22, 4.8);
    glVertex2f(22.2, 4.8);
    glVertex2f(22.2, 4.3);
    glVertex2f(22, 4.3);
    glEnd();

    // house front down 1st window 2nd h block with black border
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f); // Window color
    glVertex2f(22.2, 4.8);
    glVertex2f(22.4, 4.8);
    glVertex2f(22.4, 4.3);
    glVertex2f(22.2, 4.3);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(22.2, 4.8);
    glVertex2f(22.4, 4.8);
    glVertex2f(22.4, 4.3);
    glVertex2f(22.2, 4.3);
    glEnd();

    // house front down 1st window 3rd h block with black border
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f); // Window color
    glVertex2f(22.2, 4.3);
    glVertex2f(22.4, 4.3);
    glVertex2f(22.4, 3.8);
    glVertex2f(22.2, 3.8);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(22.2, 4.3);
    glVertex2f(22.4, 4.3);
    glVertex2f(22.4, 3.8);
    glVertex2f(22.2, 3.8);
    glEnd();

    // house front down 1st window 4th h block with black border
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f); // Window color
    glVertex2f(22, 4.3);
    glVertex2f(22.2, 4.3);
    glVertex2f(22.2, 3.8);
    glVertex2f(22, 3.8);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(22, 4.3);
    glVertex2f(22.2, 4.3);
    glVertex2f(22.2, 3.8);
    glVertex2f(22, 3.8);
    glEnd();

    // house back
    glBegin(GL_POLYGON);
    glColor3f(0.639f, 0.933f, 0.898f);

    glVertex2f(19, 5.5);
    glVertex2f(21.5, 5.5);
    glVertex2f(21.5, 3.1);
    glVertex2f(19.0021721018923, 3.0574066348317);

    glEnd();

    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color

    glVertex2f(19, 5.5);
    glVertex2f(21.5, 5.5);
    glVertex2f(21.5, 3.1);
    glVertex2f(19.0021721018923, 3.0574066348317);

    glEnd();

    // house back top part
    glBegin(GL_POLYGON);
    glColor3f(0.122f, 0.259f, 0.667f);

    glVertex2f(19, 5.5);
    glVertex2f(21.5, 5.5);
    glVertex2f(21.5, 5.3);
    glVertex2f(19.1, 5.3);

    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color

    glVertex2f(19, 5.5);
    glVertex2f(21.5, 5.5);
    glVertex2f(21.5, 5.3);
    glVertex2f(19.1, 5.3);

    glEnd();

    // house back downpart
    glBegin(GL_POLYGON);
    glColor3f(0.282f, 0.518f, 0.753f);

    glVertex2f(18.9908057372809, 3.3998093343432);
    glVertex2f(21.4979108378192, 3.3998041597796);
    glVertex2f(21.5, 3.1);
    glVertex2f(19.0021721018923, 3.0574066348317);

    glEnd();

    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color

    glVertex2f(18.9908057372809, 3.3998093343432);
    glVertex2f(21.4979108378192, 3.3998041597796);
    glVertex2f(21.5, 3.1);
    glVertex2f(19.0021721018923, 3.0574066348317);

    glEnd();

    // house back door down
    glBegin(GL_POLYGON);
    glColor3f(0.929f, 0.965f, 0.831f);

    glVertex2f(19.1, 3.6);
    glVertex2f(20.2, 3.6);
    glVertex2f(20.2, 3.4);
    glVertex2f(19.1, 3.4);

    glEnd();
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color

    glVertex2f(19.1, 3.6);
    glVertex2f(20.2, 3.6);
    glVertex2f(20.2, 3.4);
    glVertex2f(19.1, 3.4);

    glEnd();

    // house back door
    glBegin(GL_POLYGON);
    glColor3f(0.263f, 0.263f, 0.490f);

    glVertex2f(19.3, 4.3);
    glVertex2f(20, 4.3);
    glVertex2f(20, 3.6);
    glVertex2f(19.3, 3.6);

    glEnd();

    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color

    glVertex2f(19.3, 4.3);
    glVertex2f(20, 4.3);
    glVertex2f(20, 3.6);
    glVertex2f(19.3, 3.6);

    glEnd();

    // house back door top
    glBegin(GL_POLYGON);
    glColor3f(0.337f, 0.400f, 0.620f);

    glVertex2f(19.3, 4.5);
    glVertex2f(20, 4.5);
    glVertex2f(20, 4.3);
    glVertex2f(19.3, 4.3);

    glEnd();

    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(19.3, 4.5);
    glVertex2f(20, 4.5);
    glVertex2f(20, 4.3);
    glVertex2f(19.3, 4.3);
    glEnd();

    // house back window 1st block
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f);
    glVertex2f(20.7, 4.8);
    glVertex2f(20.9, 4.8);
    glVertex2f(20.9, 4.4);
    glVertex2f(20.7, 4.4);
    glEnd();

    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(20.7, 4.8);
    glVertex2f(20.9, 4.8);
    glVertex2f(20.9, 4.4);
    glVertex2f(20.7, 4.4);
    glEnd();

    // house back window 2nd block
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f);
    glVertex2f(20.9, 4.8);
    glVertex2f(21.1, 4.8);
    glVertex2f(21.1, 4.4);
    glVertex2f(20.9, 4.4);
    glEnd();

    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(20.9, 4.8);
    glVertex2f(21.1, 4.8);
    glVertex2f(21.1, 4.4);
    glVertex2f(20.9, 4.4);
    glEnd();

    // house back window 3rd block
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f);
    glVertex2f(20.9, 4.4);
    glVertex2f(21.1, 4.4);
    glVertex2f(21.1, 4);
    glVertex2f(20.9, 4);
    glEnd();

    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(20.9, 4.4);
    glVertex2f(21.1, 4.4);
    glVertex2f(21.1, 4);
    glVertex2f(20.9, 4);
    glEnd();

    // house back window 4th block
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f);
    glVertex2f(20.7, 4.4);
    glVertex2f(20.9, 4.4);
    glVertex2f(20.9, 4);
    glVertex2f(20.7, 4);
    glEnd();

    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(20.7, 4.4);
    glVertex2f(20.9, 4.4);
    glVertex2f(20.9, 4);
    glVertex2f(20.7, 4);
    glEnd();
}
void drawSmoke(){
    static float smokeY = 7.5;
    static float smokeSize = 0.3;
    static float opacity = 1.0;

    glColor4f(0.5f, 0.5f, 0.5f, opacity);
    drawFilledCircle(20.8, smokeY, smokeSize, 100);

    // Update smoke properties
    smokeY += 0.1;
    smokeSize += 0.01;
    opacity -= 0.05;

    // Reset smoke when it fades out or moves too high
    if (opacity <= 0.0f)
    {
        smokeY = 7.5;
        smokeSize = 0.3;
        opacity = 1.0;
    }
}
void updateSmoke(int value){

    glutPostRedisplay();
    drawSmoke();
    glutTimerFunc(1000, updateSmoke, 0);
}
void house_beside_forest(){

    glBegin(GL_POLYGON);
    glColor3f(0.451f, 0.863f, 0.447f);
    glVertex2f(0, 3);
    glVertex2f(2.9250602877645, 2.8330532768222);
    glVertex2f(4.8119528655895, 3.0656838686088);
    glVertex2f(7.3191936881789, 3.039836025077);
    glVertex2f(7.3191936881789, 3.039836025077);
    glVertex2f(9.9039780413638, 2.9622924944814);
    glVertex2f(12.0493490545073, 2.9105968074177);
    glVertex2f(14.6858290947559, 2.8847489638859);
    glVertex2f(19, 3);
    glVertex2f(18.9952735886179, 4.1502603619579);

    glVertex2f(0, 4);

    glEnd();

    glBegin(GL_POLYGON);
    glColor3f(0.451f, 0.863f, 0.447f);

    glVertex2f(24, 3.1);
    glVertex2f(26.705, 3.428 - 0.27);
    glVertex2f(28.230, 3.143 - 0.27);
    glVertex2f(29.961, 3.416 - 0.27);
    glVertex2f(29.9801699332932, 4.1868556413432);
    glVertex2f(24.004, 4.198);

    glEnd();
}
void first_house(){

    glLineWidth(1.0f);
    // roof
    glBegin(GL_POLYGON);
    glColor3f(0.992f, 0.224f, 0.231f); // Steel blue
    glVertex2f(6, 8);
    glVertex2f(9, 8);
    glVertex2f(10.4, 6.8);
    glVertex2f(7, 6.8);
    glEnd();

    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(6, 8);
    glVertex2f(9, 8);
    glVertex2f(10.4, 6.8);
    glVertex2f(7, 6.8);
    glEnd();

    // house front
    glBegin(GL_POLYGON);
    glColor3f(0.267f, 0.592f, 0.749f);
    glVertex2f(6, 8);
    glVertex2f(7, 6.8);
    glVertex2f(7, 4.06);
    glVertex2f(5, 4.045);
    glVertex2f(5, 6.8);
    glEnd();

    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(6, 8);
    glVertex2f(7, 6.8);
    glVertex2f(7, 4.06);
    glVertex2f(5, 4.045);
    glVertex2f(5, 6.8);
    glEnd();

    // house front top corner
    glBegin(GL_POLYGON);
    glColor3f(0.161f, 0.157f, 0.404f);
    glVertex2f(6, 8);
    glVertex2f(6.065, 7.92);
    glVertex2f(5.002, 6.64);
    glVertex2f(5, 6.8);
    glEnd();

    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(6, 8);
    glVertex2f(6.065, 7.92);
    glVertex2f(5.002, 6.64);
    glVertex2f(5, 6.8);
    glEnd();

    // house front downpart
    glBegin(GL_POLYGON);
    glColor3f(0.161f, 0.157f, 0.404f);
    glVertex2f(7.002, 4.4);
    glVertex2f(5.8, 4.4);
    glVertex2f(5.8, 4.2001);
    glVertex2f(5.9, 4.2);
    glVertex2f(5.9, 4.048);
    glVertex2f(7, 4.06);
    glEnd();

    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(7.002, 4.4);
    glVertex2f(5.8, 4.4);
    glVertex2f(5.8, 4.2001);
    glVertex2f(5.9, 4.2);
    glVertex2f(5.9, 4.048);
    glVertex2f(7, 4.06);
    glEnd();

    // house front top window
    glBegin(GL_POLYGON);
    glColor3f(0.576f, 0.776f, 0.839f);
    glVertex2f(5.8, 7);
    glVertex2f(6.2, 7);
    glVertex2f(6.2, 6.6);
    glVertex2f(5.8, 6.6);
    glEnd();

    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(5.8, 7);
    glVertex2f(6.2, 7);
    glVertex2f(6.2, 6.6);
    glVertex2f(5.8, 6.6);
    glEnd();

    // house front down 2nd window 1st block with black border
    glBegin(GL_POLYGON);
    glColor3f(0.576f, 0.776f, 0.839f);
    glVertex2f(9, 6);
    glVertex2f(9.4, 6);
    glVertex2f(9.4, 5.4);
    glVertex2f(9, 5.4);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(9, 6);
    glVertex2f(9.4, 6);
    glVertex2f(9.4, 5.4);
    glVertex2f(9, 5.4);
    glEnd();

    // house back window 2nd block
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f);
    glVertex2f(6.4, 6.2);
    glVertex2f(6.6, 6.2);
    glVertex2f(6.6, 5.6);
    glVertex2f(6.4, 5.6);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(6.4, 6.2);
    glVertex2f(6.6, 6.2);
    glVertex2f(6.6, 5.6);
    glVertex2f(6.4, 5.6);
    glEnd();

    // house back
    glBegin(GL_POLYGON);
    glColor3f(0.639f, 0.933f, 0.898f);
    glVertex2f(7, 6.8);
    glVertex2f(10.4, 6.8);
    glVertex2f(10.4, 4.084);
    glVertex2f(7, 4.06);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(7, 6.8);
    glVertex2f(10.4, 6.8);
    glVertex2f(10.4, 4.084);
    glVertex2f(7, 4.06);
    glEnd();

    // house back top part
    glBegin(GL_POLYGON);
    glColor3f(0.122f, 0.259f, 0.667f);
    glVertex2f(7, 6.8);
    glVertex2f(10.4, 6.8);
    glVertex2f(10.4, 6.6);
    glVertex2f(7.001, 6.602);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(7, 6.8);
    glVertex2f(10.4, 6.8);
    glVertex2f(10.4, 6.6);
    glVertex2f(7.001, 6.602);
    glEnd();

    // house back downpart
    glBegin(GL_POLYGON);
    glColor3f(0.282f, 0.518f, 0.753f);
    glVertex2f(7.002, 4.4);
    glVertex2f(10.4, 4.4);
    glVertex2f(10.4, 4.084);
    glVertex2f(7, 4.06);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(7.002, 4.4);
    glVertex2f(10.4, 4.4);
    glVertex2f(10.4, 4.084);
    glVertex2f(7, 4.06);
    glEnd();

    // house back door down
    glBegin(GL_POLYGON);
    glColor3f(0.929f, 0.965f, 0.831f);
    glVertex2f(5, 4.2);
    glVertex2f(5.9, 4.2);
    glVertex2f(5.9, 4.048);
    glVertex2f(5, 4.045);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(5, 4.2);
    glVertex2f(5.9, 4.2);
    glVertex2f(5.9, 4.048);
    glVertex2f(5, 4.045);
    glEnd();

    // house back door down upppp
    glBegin(GL_POLYGON);
    glColor3f(0.929f, 0.965f, 0.831f);
    glVertex2f(5.2, 4.4);
    glVertex2f(5.8, 4.4);
    glVertex2f(5.8, 4.2001);
    glVertex2f(5.2, 4.2001);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(5.2, 4.4);
    glVertex2f(5.8, 4.4);
    glVertex2f(5.8, 4.2001);
    glVertex2f(5.2, 4.2001);
    glEnd();

    // house back door
    glBegin(GL_POLYGON);
    glColor3f(0.263f, 0.263f, 0.490f);
    glVertex2f(5.3, 5.4);
    glVertex2f(5.8, 5.4);
    glVertex2f(5.8, 4.4);
    glVertex2f(5.3, 4.4005);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(5.3, 5.4);
    glVertex2f(5.8, 5.4);
    glVertex2f(5.8, 4.4);
    glVertex2f(5.3, 4.4005);
    glEnd();

    // house back window 1st block
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f);
    glVertex2f(6.2, 6.2);
    glVertex2f(6.4, 6.2);
    glVertex2f(6.4, 5.6);
    glVertex2f(6.2, 5.6);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(6.2, 6.2);
    glVertex2f(6.4, 6.2);
    glVertex2f(6.4, 5.6);
    glVertex2f(6.2, 5.6);
    glEnd();

    // house front window 2nd 1st block
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f);
    glVertex2f(9, 6);
    glVertex2f(9.4, 6);
    glVertex2f(9.4, 5.4);
    glVertex2f(9, 5.4);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(9, 6);
    glVertex2f(9.4, 6);
    glVertex2f(9.4, 5.4);
    glVertex2f(9, 5.4);

    glEnd();

    // house front window 2nd 2nd block
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f);
    glVertex2f(9.4, 6);
    glVertex2f(9.8, 6);
    glVertex2f(9.8, 5.4);
    glVertex2f(9.4, 5.4);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(9.4, 6);
    glVertex2f(9.8, 6);
    glVertex2f(9.8, 5.4);
    glVertex2f(9.4, 5.4);
    glEnd();

    // house front window 2nd 3rd block
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f);
    glVertex2f(9.4, 5.4);
    glVertex2f(9.8, 5.4);
    glVertex2f(9.8, 4.8);
    glVertex2f(9.4, 4.8);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(9.4, 5.4);
    glVertex2f(9.8, 5.4);
    glVertex2f(9.8, 4.8);
    glVertex2f(9.4, 4.8);

    glEnd();

    // house front window 2nd 4th block
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f);
    glVertex2f(9, 5.4);
    glVertex2f(9.4, 5.4);
    glVertex2f(9.4, 4.8);
    glVertex2f(9, 4.8);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(9, 5.4);
    glVertex2f(9.4, 5.4);
    glVertex2f(9.4, 4.8);
    glVertex2f(9, 4.8);

    glEnd();

    // house front window 1st 1st block
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f);
    glVertex2f(7.6, 6);
    glVertex2f(8, 6);
    glVertex2f(8, 5.4);
    glVertex2f(7.6, 5.4);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(7.6, 6);
    glVertex2f(8, 6);
    glVertex2f(8, 5.4);
    glVertex2f(7.6, 5.4);

    glEnd();

    // house front window 1st 2nd block
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f);
    glVertex2f(8, 6);
    glVertex2f(8.4, 6);
    glVertex2f(8.4, 5.4);
    glVertex2f(8, 5.4);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(8, 6);
    glVertex2f(8.4, 6);
    glVertex2f(8.4, 5.4);
    glVertex2f(8, 5.4);
    glEnd();

    // house front window 2nd 3rd block
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f);
    glVertex2f(8, 5.4);
    glVertex2f(8.4, 5.4);
    glVertex2f(8.4, 4.8);
    glVertex2f(8, 4.8);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(8, 5.4);
    glVertex2f(8.4, 5.4);
    glVertex2f(8.4, 4.8);
    glVertex2f(8, 4.8);
    glEnd();

    // house front window 1st 4th block
    glBegin(GL_POLYGON);
    glColor3f(0.373f, 0.694f, 0.847f);
    glVertex2f(7.6, 5.4);
    glVertex2f(8, 5.4);
    glVertex2f(8, 4.8);
    glVertex2f(7.6, 4.8);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(7.6, 5.4);
    glVertex2f(8, 5.4);
    glVertex2f(8, 4.8);
    glVertex2f(7.6, 4.8);

    glEnd();
}
void second_house(){

    glLineWidth(1.0f);
    // roof
    glBegin(GL_POLYGON);
    glColor3f(0.9843f, 0.2431f, 0.2549f);
    glVertex2f(11.9, 8.1);
    glVertex2f(14.8, 8.1);
    glVertex2f(16, 6.8);
    glVertex2f(12.8, 6.8);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(11.9, 8.1);
    glVertex2f(14.8, 8.1);
    glVertex2f(16, 6.8);
    glVertex2f(12.8, 6.8);
    glEnd();

    // roof down part
    glBegin(GL_POLYGON);
    glColor3f(0.1373f, 0.2392f, 0.4980f);
    glVertex2f(12.8, 6.8);
    glVertex2f(16, 6.8);
    glVertex2f(16.1, 6.65);
    glVertex2f(12.9, 6.65);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(12.8, 6.8);
    glVertex2f(16, 6.8);
    glVertex2f(16.1, 6.65);
    glVertex2f(12.9, 6.65);
    glEnd();

    // house chimni front part
    glBegin(GL_POLYGON);
    glColor3f(0.6353f, 0.8235f, 0.8471f);
    glVertex2f(13.6, 8.6);
    glVertex2f(14, 8.6);
    glVertex2f(14, 7.4);
    glVertex2f(13.6, 7.4);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(13.6, 8.6);
    glVertex2f(14, 8.6);
    glVertex2f(14, 7.4);
    glVertex2f(13.6, 7.4);
    glEnd();

    // house chimni back part
    glBegin(GL_POLYGON);
    glColor3f(0.2196f, 0.2039f, 0.3961f);
    glVertex2f(14, 8.6);
    glVertex2f(14.2, 8.6);
    glVertex2f(14.2, 7.6);
    glVertex2f(14, 7.4);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(14, 8.6);
    glVertex2f(14.2, 8.6);
    glVertex2f(14.2, 7.6);
    glVertex2f(14, 7.4);
    glEnd();

    // house chimni front upper part
    glBegin(GL_POLYGON);
    glColor3f(0.9569f, 0.9608f, 0.8157f);
    glVertex2f(13.6, 8.8);
    glVertex2f(14, 8.8);
    glVertex2f(14, 8.6);
    glVertex2f(13.6, 8.6);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(13.6, 8.8);
    glVertex2f(14, 8.8);
    glVertex2f(14, 8.6);
    glVertex2f(13.6, 8.6);
    glEnd();

    // house chimni back upper part
    glBegin(GL_POLYGON);
    glColor3f(0.5451f, 0.6471f, 0.6314f);
    glVertex2f(14, 8.8);
    glVertex2f(14.2, 8.7);
    glVertex2f(14.2, 8.6);
    glVertex2f(14, 8.6);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(14, 8.8);
    glVertex2f(14.2, 8.7);
    glVertex2f(14.2, 8.6);
    glVertex2f(14, 8.6);
    glEnd();

    // house back part
    glBegin(GL_POLYGON);
    glColor3f(0.2235f, 0.5843f, 0.7529f);
    glVertex2f(12.8, 6.8);
    glVertex2f(16, 6.8);
    glVertex2f(16, 4.3);
    glVertex2f(12.8, 4.3);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(12.8, 6.8);
    glVertex2f(16, 6.8);
    glVertex2f(16, 4.3);
    glVertex2f(12.8, 4.3);
    glEnd();

    // back part window 1st 1st block
    glBegin(GL_POLYGON);
    glColor3f(0.5647f, 0.7804f, 0.9255f);
    glVertex2f(13.4, 5.8);
    glVertex2f(13.6, 5.8);
    glVertex2f(13.6, 5.4);
    glVertex2f(13.4, 5.4);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(13.4, 5.8);
    glVertex2f(13.6, 5.8);
    glVertex2f(13.6, 5.4);
    glVertex2f(13.4, 5.4);
    glEnd();

    // back part window 1st 2nd block
    glBegin(GL_POLYGON);
    glColor3f(0.5647f, 0.7804f, 0.9255f);
    glVertex2f(13.6, 5.8);
    glVertex2f(13.8, 5.8);
    glVertex2f(13.8, 5.4);
    glVertex2f(13.6, 5.4);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0, 0, 0);
    glVertex2f(13.6, 5.8);
    glVertex2f(13.8, 5.8);
    glVertex2f(13.8, 5.4);
    glVertex2f(13.6, 5.4);
    glEnd();

    // back part window 1st 3rd block
    glBegin(GL_POLYGON);
    glColor3f(0.5647f, 0.7804f, 0.9255f);
    glVertex2f(13.6, 5.4);
    glVertex2f(13.8, 5.4);
    glVertex2f(13.8, 5);
    glVertex2f(13.6, 5);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(13.6, 5.4);
    glVertex2f(13.8, 5.4);
    glVertex2f(13.8, 5);
    glVertex2f(13.6, 5);
    glEnd();

    // back part window 1st 4th block
    glBegin(GL_POLYGON);
    glColor3f(0.5647f, 0.7804f, 0.9255f);
    glVertex2f(13.4, 5.4);
    glVertex2f(13.6, 5.4);
    glVertex2f(13.6, 5);
    glVertex2f(13.4, 5);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(13.4, 5.4);
    glVertex2f(13.6, 5.4);
    glVertex2f(13.6, 5);
    glVertex2f(13.4, 5);
    glEnd();

    // back part window 2nd 1st block
    glBegin(GL_POLYGON);
    glColor3f(0.5647f, 0.7804f, 0.9255f);
    glVertex2f(14.8, 5.8);
    glVertex2f(15, 5.8);
    glVertex2f(15, 5.4);
    glVertex2f(14.8, 5.4);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(14.8, 5.8);
    glVertex2f(15, 5.8);
    glVertex2f(15, 5.4);
    glVertex2f(14.8, 5.4);
    glEnd();

    // back part window 2nd 2nd block
    glBegin(GL_POLYGON);
    glColor3f(0.5647f, 0.7804f, 0.9255f);
    glVertex2f(15, 5.8);
    glVertex2f(15.2, 5.8);
    glVertex2f(15.2, 5.4);
    glVertex2f(15, 5.4);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(15, 5.8);
    glVertex2f(15.2, 5.8);
    glVertex2f(15.2, 5.4);
    glVertex2f(15, 5.4);
    glEnd();

    // back part window 2nd 3rd block
    glBegin(GL_POLYGON);
    glColor3f(0.5647f, 0.7804f, 0.9255f);
    glVertex2f(15, 5.4);
    glVertex2f(15.2, 5.4);
    glVertex2f(15.2, 5);
    glVertex2f(15, 5);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(15, 5.4);
    glVertex2f(15.2, 5.4);
    glVertex2f(15.2, 5);
    glVertex2f(15, 5);
    glEnd();

    // back part window 2nd 4th block
    glBegin(GL_POLYGON);
    glColor3f(0.5647f, 0.7804f, 0.9255f);
    glVertex2f(14.8, 5.4);
    glVertex2f(15, 5.4);
    glVertex2f(15, 5);
    glVertex2f(14.8, 5);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(14.8, 5.4);
    glVertex2f(15, 5.4);
    glVertex2f(15, 5);
    glVertex2f(14.8, 5);
    glEnd();

    // back part down
    glBegin(GL_POLYGON);
    glColor3f(0.282f, 0.518f, 0.753f);
    glVertex2f(12.8, 4.3);
    glVertex2f(16, 4.3);
    glVertex2f(15.9991, 4.126);
    glVertex2f(12.8, 4.1015);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(12.8, 4.3);
    glVertex2f(16, 4.3);
    glVertex2f(15.9991, 4.126);
    glVertex2f(12.8, 4.1015);
    glEnd();

    // front
    glBegin(GL_POLYGON);
    glColor3f(0.6118f, 0.9059f, 0.8549f);
    glVertex2f(11, 6.8);
    glVertex2f(11.9, 8.1);
    glVertex2f(12.8, 6.8);
    glVertex2f(12.8, 4.1015);
    glVertex2f(11.0012876337854, 4.0952324079706);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(11, 6.8);
    glVertex2f(11.9, 8.1);
    glVertex2f(12.8, 6.8);
    glVertex2f(12.8, 4.1015);
    glVertex2f(11.0012876337854, 4.0952324079706);
    glEnd();

    // front upper part
    glBegin(GL_POLYGON);
    glColor3f(0.1020f, 0.1882f, 0.5176f);
    glVertex2f(11, 6.8);
    glVertex2f(11.9, 8.1);
    glVertex2f(11.948, 8.03);
    glVertex2f(11.000052, 6.69);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(11, 6.8);
    glVertex2f(11.9, 8.1);
    glVertex2f(11.948, 8.03);
    glVertex2f(11.000052, 6.69);
    glEnd();

    // front window
    glBegin(GL_POLYGON);
    glColor3f(0.4667f, 0.8000f, 0.8627f);
    glVertex2f(11.6, 7);
    glVertex2f(12, 7);
    glVertex2f(12, 6.6);
    glVertex2f(11.6, 6.6);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(11.6, 7);
    glVertex2f(12, 7);
    glVertex2f(12, 6.6);
    glVertex2f(11.6, 6.6);
    glEnd();

    // front door
    glBegin(GL_POLYGON);
    glColor3f(0.2667f, 0.2627f, 0.5020f);
    glVertex2f(11.2, 5.4);
    glVertex2f(11.6, 5.4);
    glVertex2f(11.6, 4.3);
    glVertex2f(11.2, 4.3);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(11.2, 5.4);
    glVertex2f(11.6, 5.4);
    glVertex2f(11.6, 4.3);
    glVertex2f(11.2, 4.3);
    glEnd();

    // front door down part
    glBegin(GL_POLYGON);
    glColor3f(0.9255f, 0.9569f, 0.8157f);
    glVertex2f(11.001, 4.2);
    glVertex2f(11.75, 4.2);
    glVertex2f(11.7515559278993, 4.0885646145288);
    glVertex2f(11.0012876337854, 4.0952324079706);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(11.001, 4.2);
    glVertex2f(11.75, 4.2);
    glVertex2f(11.7515559278993, 4.0885646145288);
    glVertex2f(11.0012876337854, 4.0952324079706);
    glEnd();

    // front door down upper part
    glBegin(GL_POLYGON);
    glColor3f(0.9255f, 0.9569f, 0.8157f);
    glVertex2f(11.1, 4.3);
    glVertex2f(11.7, 4.3);
    glVertex2f(11.7, 4.2);
    glVertex2f(11.1, 4.2);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(11.1, 4.3);
    glVertex2f(11.7, 4.3);
    glVertex2f(11.7, 4.2);
    glVertex2f(11.1, 4.2);
    glEnd();

    // front down part
    glBegin(GL_POLYGON);
    glColor3f(0.2000f, 0.2627f, 0.4196f);
    glVertex2f(11.7, 4.3);
    glVertex2f(12.8, 4.3);
    glVertex2f(12.8, 4.1015);
    glVertex2f(11.7515559278993, 4.0885646145288);
    glVertex2f(11.75, 4.2);
    glVertex2f(11.7, 4.2);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(11.001, 4.2);
    glVertex2f(11.75, 4.2);
    glVertex2f(11.7515559278993, 4.0885646145288);
    glVertex2f(11.0012876337854, 4.0952324079706);
    glEnd();

    // back upper part
    glBegin(GL_POLYGON);
    glColor3f(0.1255f, 0.2431f, 0.6588f);
    glVertex2f(12.8, 6.8);
    glVertex2f(16, 6.8);
    glVertex2f(16.1, 6.65);
    glVertex2f(12.9, 6.65);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(12.8, 6.8);
    glVertex2f(16, 6.8);
    glVertex2f(16.1, 6.65);
    glVertex2f(12.9, 6.65);
    glEnd();
}
void background_gradient(){
    glBegin(GL_QUADS);
    glColor3f(0.6f, 0.8f, 1.0f);   // Light sky blue at the bottom-left corner
    glVertex2f(0.0f, 0.0f);        // Bottom-left corner
    glColor3f(0.85f, 0.93f, 1.0f); // Very light blue at the top-left corner
    glVertex2f(0.0f, 15.0f);       // Top-left corner
    glColor3f(0.85f, 0.93f, 1.0f); // Very light blue at the top-right corner
    glVertex2f(30.0f, 15.0f);      // Top-right corner
    glColor3f(0.6f, 0.8f, 1.0f);   // Light sky blue at the bottom-right corner
    glVertex2f(30.0f, 0.0f);       // Bottom-right corner
    glEnd();
}
void left_side_circle(){

    glColor3f(0.310, 0.729, 0.478);

    drawTiltedFilledSemiCircle(0.373419194651, 4.0033375294768, 0.96, 0, true);
}
void house_behind_circle(){

    glColor3f(0.451f, 0.863f, 0.447f);
    drawTiltedFilledSemiCircle(24.1630819759195, 4.192631737198, 5.15, .96, true);
}
void house_behind_circle_2(){

    glColor3f(0.310, 0.729, 0.478);

    drawTiltedFilledSemiCircle(24.8321393434664, 5.258631566017, 4.28, .96, true);
}
void circle_behind_the_second_house(){

    glColor3f(0.451f, 0.863f, 0.447f);

    drawTiltedFilledSemiCircle(15.3704596740181, 4.7220976705139, 4.63, .96, true);
}


void a_mouse(int button, int state, int x, int y){
    if (button == GLUT_LEFT_BUTTON && state == GLUT_DOWN)
    {
        isSpinning = false; // Stop spinning
    }
}

void a_keyboard(unsigned char key, int x, int y){
    if (key == 'r' || key == 'R')
    {
        isSpinning = true; // Start spinning
    }
}

void turbine(float rotationAngle){
    // Body part (No rotation required)
    glBegin(GL_POLYGON);
    glColor3f(0.400, 0.392, 0.396);

    glVertex2f(16.99, 8.13);
    glVertex2f(17.20, 8.13);
    glVertex2f(17.2037489307553, 10.4994645691834);
    glVertex2f(17, 10.5);
    glEnd();

    // circle part
    glColor3f(0.722, 0.722, 0.722);
    drawFilledCircle(17.09, 10.71, 0.22, 100);

    // Rotate and draw the 1st wheel
    glPushMatrix();
    glTranslatef(17.2f, 10.7f, 0.0f);
    glRotatef(rotationAngle, 0.0f, 0.0f, 1.0f);
    glTranslatef(-17.2f, -10.7f, 0.0f);

    glBegin(GL_POLYGON);
    glColor3f(0.722, 0.722, 0.722);
    glVertex2f(17.12, 10.62);
    glVertex2f(18.64, 10.2);
    glVertex2f(17.15, 10.75);
    glEnd();
    glPopMatrix();

    // Rotate and draw the 2nd wheel
    glPushMatrix();
    glTranslatef(17.1f, 10.8f, 0.0f);
    glRotatef(rotationAngle, 0.0f, 0.0f, 1.0f);
    glTranslatef(-17.1f, -10.8f, 0.0f);

    glBegin(GL_POLYGON);
    glColor3f(0.722, 0.722, 0.722);
    glVertex2f(17.2, 10.76);
    glVertex2f(17.1, 12.1);
    glVertex2f(17.02, 10.76);
    glEnd();
    glPopMatrix();

    // Rotate and draw the 3rd wheel
    glPushMatrix();
    glTranslatef(17.0f, 10.7f, 0.0f);
    glRotatef(rotationAngle, 0.0f, 0.0f, 1.0f);
    glTranslatef(-17.0f, -10.7f, 0.0f);

    glBegin(GL_POLYGON);
    glColor3f(0.722, 0.722, 0.722);
    glVertex2f(17.05, 10.75);
    glVertex2f(15.7, 10.1);
    glVertex2f(17.05, 10.6);
    glEnd();
    glPopMatrix();
}
void turbine_timer(int value){

    glutPostRedisplay();

    glutTimerFunc(30, turbine_timer, 0);
}
void third_turbine(){
    // Body down part
    glBegin(GL_POLYGON);
    glColor3f(0.400, 0.392, 0.396);
    glVertex2f(25.4, 10.04);
    glVertex2f(25.6, 9.88);
    glVertex2f(25.6, 11.4);
    glVertex2f(25.4, 11.4);
    glEnd();

    // Top circle
    glColor3f(0.722, 0.722, 0.722);
    drawFilledCircle(25.5, 11.6, 0.22, 100);


    glPushMatrix();


    glTranslatef(25.5, 11.6, 0.0f);

    // Rotate the wheels
    glRotatef(rotationAngle, 0.0f, 0.0f, 1.0f);

    // 1st wheel
    glBegin(GL_POLYGON);
    glColor3f(0.722, 0.722, 0.722);
    glVertex2f(0.1, -0.2);
    glVertex2f(1.1, -0.6);
    glVertex2f(0.1, 0.0);
    glEnd();

    // 2nd wheel
    glBegin(GL_POLYGON);
    glColor3f(0.722, 0.722, 0.722);
    glVertex2f(0.1, 0.06);
    glVertex2f(0.0, 1.1);
    glVertex2f(-0.1, 0.06);
    glEnd();

    // 3rd wheel
    glBegin(GL_POLYGON);
    glColor3f(0.722, 0.722, 0.722);
    glVertex2f(-0.1225637798323, 0.04220853633);
    glVertex2f(-1.1, -0.6);
    glVertex2f(-0.02, -0.08);
    glEnd();

    // Restore the matrix
    glPopMatrix();
}
void house_behind_bush(){
    glColor3f(0.451f, 0.863f, 0.447f); // Set color to green
    drawTiltedFilledSemiCircle(22.7122696509239, 4.1401343960566, 3.75, 0.96, true);
}
void house_behind_box(){
    glBegin(GL_POLYGON);
    glColor3f(0.451f, 0.863f, 0.447f);
    glVertex2f(0, 4);
    glVertex2f(29.9801699332932, 4.1868556413432);
    glVertex2f(29.9876, 8.23);
    glVertex2f(0, 8);
    glEnd();

    glBegin(GL_POLYGON);
    glColor3f(0.204f, 0.620f, 0.647f);

    glVertex2f(0, 8);
    glVertex2f(6, 8);
    glVertex2f(5, 6.8);
    glVertex2f(5, 4.045);
    glVertex2f(0, 4);
    glEnd();

    // bush
    glColor3f(0.204f, 0.620f, 0.647f);
    drawFilledCircle(0.2370771636974, 7.7447874048603, 0.58, 100);
    drawFilledCircle(1.1368645436581, 7.5795920655732, 0.7, 100);
    drawFilledCircle(2.3139001028987, 7.2712669728099, 1.16, 100);
    // drawFilledCircle(3.2476841705224, 7.0527217654971,1.16,100);
}


void first_mountain(){
    // 1st tower
    glBegin(GL_POLYGON);
    glColor3f(0.325, 0.831, 0.604);
    glVertex2f(3.6, 8.03);
    glVertex2f(11, 14.5);
    glVertex2f(11.0077757318667, 8.0649685230255);
    glEnd();


            // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
   glVertex2f(3.6, 8.03);
    glVertex2f(11, 14.5);
   // glVertex2f(11.0077757318667, 8.0649685230255);
    glEnd();

    // 2nd tower
    glBegin(GL_POLYGON);
    glColor3f(0.176, 0.459, 0.624);
    glVertex2f(11, 14.5);
    glVertex2f(18.4805934483466, 8.1422483740147);
    glVertex2f(11.0077757318667, 8.0649685230255);
    glEnd();



               // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(11, 14.5);
    glVertex2f(18.4805934483466, 8.1422483740147);
    glVertex2f(11.0077757318667, 8.0649685230255);
    glEnd();

    // mid short mountain
    glBegin(GL_POLYGON);
    glColor3f(0.239, 0.616, 0.702);

    glVertex2f(11.004, 11.32);
    glVertex2f(13.43, 8.1032);
    glVertex2f(11.0077757318667, 8.0649685230255);
    glEnd();





               // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color

    glVertex2f(11.004, 11.32);
    glVertex2f(13.43, 8.1032);
    glVertex2f(11.0077757318667, 8.0649685230255);
    glEnd();

    // top white part
    glBegin(GL_POLYGON);
    glColor3f(1, 1, 1);

    glVertex2f(9.06, 12.8);
    glVertex2f(11.003, 12.2);
    glVertex2f(11, 14.5);
    glEnd();

    drawFilledCircle(10.5374575826067, 12.3161804530956, 0.44, 100);
    drawFilledCircle(10, 12.5, 0.35, 100);
    drawFilledCircle(9.3586618033266, 12.6084361597528, 0.35, 100);
}
void second_mountain(){
    // 1st tower
    glBegin(GL_POLYGON);
    glColor3f(0.325, 0.831, 0.604);
    glVertex2f(14.5, 8.11);
    glVertex2f(21.5, 13);
    glVertex2f(21.5, 8.17);
    glEnd();

       // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
  glVertex2f(14.5, 8.11);
    glVertex2f(21.5, 13);
    //glVertex2f(21.5, 8.17);
    glEnd();

    // 2nd tower
    glBegin(GL_POLYGON);
    glColor3f(0.176, 0.459, 0.624);
    glVertex2f(21.5, 13);
    glVertex2f(27.8, 8.21);
    glVertex2f(21.5, 8.17);
    glEnd();


           // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
   glVertex2f(21.5, 13);
    glVertex2f(27.8, 8.21);
    glVertex2f(21.5, 8.17);
    glEnd();

    // mid short mountain
    glBegin(GL_POLYGON);
    glColor3f(0.239, 0.616, 0.702);

    glVertex2f(21.5, 10.5);
    glVertex2f(24, 8.18);
    glVertex2f(21.5, 8.17);
    glEnd();


               // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(21.5, 10.5);
    glVertex2f(24, 8.18);
    glVertex2f(21.5, 8.17);
    glEnd();

    // top white part
    glBegin(GL_POLYGON);
    glColor3f(1, 1, 1);

    glVertex2f(20.20, 12.1);
    glVertex2f(21.5, 11.5);
    glVertex2f(21.5, 13);
    glEnd();

    drawFilledCircle(20.9412750351478, 11.9324194141677, 0.53, 100);
    drawFilledCircle(20.5, 12, 0.26, 100);
}




void first_turbine() {
    glLineWidth(1);
    // Body down part
    glBegin(GL_POLYGON);
    glColor3f(0.400, 0.392, 0.396);
    glVertex2f(5.4, 9.6);
    glVertex2f(5.4, 11.6);
    glVertex2f(5.2, 11.6);
    glVertex2f(5.2, 9.4);
    glEnd();


    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(5.4, 9.6);
    glVertex2f(5.4, 11.6);
    glVertex2f(5.2, 11.6);
    glVertex2f(5.2, 9.4);
    glEnd();

    // Top circle
    glColor3f(0.722, 0.722, 0.722);
    drawFilledCircle(5.2829725683304, 11.7646090338199, 0.23, 100);


    glPushMatrix();

    // Translate to the turbine's center
    glTranslatef(5.2829725683304, 11.7646090338199, 0.0f);

    // Rotate the blades
    glRotatef(rotationAngle, 0.0f, 0.0f, 1.0f);

    // Draw the three blades
    glColor3f(0.722, 0.722, 0.722);

    glBegin(GL_POLYGON); // 1st blade
    glVertex2f(0.05, -0.15);
    glVertex2f(1.2, -0.6);
    glVertex2f(0.1, 0.0);
    glEnd();

    glBegin(GL_POLYGON); // 2nd blade
    glVertex2f(0.05, 0.15);
    glVertex2f(0.0, 1.2);
    glVertex2f(-0.10, 0.15);
    glEnd();

    glBegin(GL_POLYGON); // 3rd blade
    glVertex2f(-0.05, 0.0);
    glVertex2f(-1.2, -0.6);
    glVertex2f(-0.05, -0.15);
    glEnd();


    glPopMatrix();
}







float fruitY = 7.106;
float fallSpeed = 0.05;

void updateFruitPosition() {
    fruitY -= fallSpeed;


    if (fruitY < 3.0f) {
        fruitY = 7.0f;
    }
}






void arman_tree()
{
        glBegin(GL_POLYGON);
      glColor3f(0.545, 0.373, 0.216);

    glVertex2f(26.8, 3);
    glVertex2f(27.5, 3);
    glVertex2f(27.5, 6.5);
      glVertex2f(26.8, 6.5);

    glEnd();

    glColor3f(0.514, 0.776, 0.278);

    drawFilledCircle(26.4735234944006, 6.4149241608664,0.85,100);


    drawFilledCircle(25.8900203666516, 6.8379639284779,0.85,100);

    drawFilledCircle(25.9046079448453, 7.4652297907985,0.92,100);

    drawFilledCircle(26.8382129492436, 8.004970183958,0.9,100);
    drawFilledCircle(27.8593434228043, 7.654868307314,1.08,100);
      drawFilledCircle(28.1656825648726, 6.9692521322194,0.96,100);


      //fruit 1
      glColor3f(1.0f, 0.0f, 0.2f);

      drawFilledCircle(25.4610527698265, 7.1063084499539,0.15,100);
       //fruit 2
        drawFilledCircle(26.1192367693426, 7.8233544331555,0.15,100);
         //fruit 3
        drawFilledCircle(27.3973549301796, 8.3222324055366,0.15,100);
         //fruit 4
        drawFilledCircle(27.5854352723296, 7.0688508239657,0.15,100);



        // Animate only one fruit
    glColor3f(1.0f, 0.0f, 0.2f);
    drawFilledCircle(25.4610527698265, fruitY, 0.15, 100); // y position





//fruit stem 1st
             glBegin(GL_POLYGON);
      glColor3f(0.545, 0.373, 0.216);

    glVertex2f(25.49,7.25);
    glVertex2f(25.5862477512758, 7.3453691568107);
    glVertex2f(25.6453951945558, 7.2760238784835);
      glVertex2f(25.5719707822083, 7.2209555692237);

    glEnd();






//fruit stem 2nd
             glBegin(GL_POLYGON);
      glColor3f(0.545, 0.373, 0.216);

    glVertex2f(26.1,7.97);
    glVertex2f(26.1, 8.1);
    glVertex2f(26.1566066957566, 8.0987807368751);
      glVertex2f(26.15, 7.96);

    glEnd();



//fruit stem 3rd
             glBegin(GL_POLYGON);
      glColor3f(0.545, 0.373, 0.216);

    glVertex2f(27.45,8.5);
    glVertex2f(27.510370818306, 8.5684392694614);
    glVertex2f(27.5549666768832, 8.5229857982199);
      glVertex2f(27.5146588816307, 8.4766747143135);

    glEnd();


//fruit stem 4th
             glBegin(GL_POLYGON);
      glColor3f(0.545, 0.373, 0.216);

    glVertex2f(27.6,7.21);
    glVertex2f(27.6615182431903, 7.3139026144305);
    glVertex2f(27.7197525578999, 7.2777571777148);
      glVertex2f(27.66, 7.2);

    glEnd();

}



void fruit_timer(int value) {
    updateFruitPosition();

    glutPostRedisplay();
    //glutTimerFunc(100, fruit_timer, 0);
}


void first_tree()
{

        glBegin(GL_POLYGON);
     glColor3f(0.357, 0.298, 0.129);


    glVertex2f(1.4, 4.01);
    glVertex2f(1.6000051712834, 4.0106577915428);
    glVertex2f(1.6, 5.6);
      glVertex2f(1.4, 5.6);

    glEnd();


    glColor3f(0.427, 0.655, 0.263);

drawFilledCircle(1.4799369775451, 5.907786693143,0.6,100);
glColor3f(0.643, 0.812, 0.349);

drawFilledCircle(1.0206595751814, 5.8754233928482,0.47,100);
drawFilledCircle(1.8782142481178, 5.6332364779152,0.28,100);
glColor3f(0.8, 0.875, 0.506);

drawFilledCircle(1.7553925976965, 5.7074611722037,0.14,100);


//stem
    glBegin(GL_POLYGON);
       glColor3f(0.357, 0.298, 0.129);


    glVertex2f(1.6, 5.3);
    glVertex2f(1.9, 5.7);
    glVertex2f(1.95, 5.7);
      glVertex2f(1.6, 5.25);

    glEnd();


    //stem
       glBegin(GL_POLYGON);
       glColor3f(0.357, 0.298, 0.129);


    glVertex2f(1.6, 5.45);
    glVertex2f(1.6, 5.5);
    glVertex2f(1.1, 6);
      glVertex2f(1.05, 6);

    glEnd();
}



void second_tree()
{
    glLineWidth(1);

         glBegin(GL_POLYGON);
       glColor3f(0.357, 0.298, 0.129);


    glVertex2f(3.2116004848449, 4.0310211125502);
    glVertex2f(4, 4.03);
    glVertex2f(4, 5);
      glVertex2f(3.2, 5);

    glEnd();





        glBegin(GL_POLYGON);
      glColor3f(0.427, 0.655, 0.263);
    glVertex2f(2.6, 5);
    glVertex2f(4.6, 5);
    glVertex2f(3.6, 6);
    glEnd();



       glBegin(GL_POLYGON);
      glColor3f(0.427, 0.655, 0.263);
    glVertex2f(2.7830933474082, 5.5085539216383);
    glVertex2f(4.4836137401549, 5.5018982645239);
    glVertex2f(3.6, 6.6);
    glEnd();



        glBegin(GL_POLYGON);
      glColor3f(0.427, 0.655, 0.263);
    glVertex2f(3, 6.2);
    glVertex2f(4.2, 6.2);
    glVertex2f(3.6, 7.2);
    glEnd();



}


void third_tree()
{



         glBegin(GL_POLYGON);
       glColor3f(0.357, 0.298, 0.129);


    glVertex2f(17.2, 4.14);
    glVertex2f(17.8, 4.14);
    glVertex2f(17.8, 5.6);
      glVertex2f(17.2, 5.6);

    glEnd();





        glBegin(GL_POLYGON);
      glColor3f(0.427, 0.655, 0.263);
    glVertex2f(16.6, 5.4);
    glVertex2f(18.4, 5.4);
    glVertex2f(17.5, 6.4);
    glEnd();



       glBegin(GL_POLYGON);
      glColor3f(0.427, 0.655, 0.263);
    glVertex2f(16.8, 6);
    glVertex2f(18.2, 6);
    glVertex2f(17.4, 7);
    glEnd();



        glBegin(GL_POLYGON);
      glColor3f(0.427, 0.655, 0.263);
    glVertex2f(16.8, 6.6);
    glVertex2f(18, 6.6);
    glVertex2f(17.4, 7.4);
    glEnd();



}



void arman_cloud()
{

    //box
    glBegin(GL_POLYGON);
      glColor3f(1,1,1);
    glVertex2f(2, 13);
    glVertex2f(6, 13);
    glVertex2f(6, 14);
    glVertex2f(2, 14);
    glEnd();


    drawFilledCircle(1.8440512055707, 13.6146194158868,0.66,100);
    drawFilledCircle(2.6530765328939, 13.8701842158852,0.6,100);
    drawFilledCircle(3.6437616119729, 14.0735848166872,0.66,100);
    drawFilledCircle(4.7784856923838, 13.8331243786111,0.61,100);
    drawFilledCircle(5.8598273842822, 13.6215118925605,0.6,100);;
}


void arman_cloud2()
{
        //box
    glBegin(GL_POLYGON);
      glColor3f(1,1,1);
    glVertex2f(19, 13);
    glVertex2f(21, 13);
    glVertex2f(21, 14);
    glVertex2f(19, 14);
    glEnd();


    drawFilledCircle(18.8814360833634, 13.57788778033,0.6,100);
    drawFilledCircle(19.2754922283317, 13.845044488779,0.58,100);
    drawFilledCircle(19.7363375504133, 14.0587698555382,0.62,100);
    drawFilledCircle(20.52444984035, 13.931870419025,0.6,100);
    drawFilledCircle(21.0921578458128, 13.5912456157525,0.6,100);;
}







void a_display()
{
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
     background_gradient();

    house_behind_bush();
    second_mountain();
    first_mountain();

    house_behind_box();
    path();
    house_behind_circle_2();

    house_behind_circle();
    circle_behind_the_second_house();
    // house_behind_circle_3();

    house();

    path_down_ground();
    house_beside_forest();

    first_house();
    second_house();
    //Cloud1();
    left_side_circle();

    if (isSpinning)
    {
        rotationAngle += rotationSpeed;
        if (rotationAngle >= 360.0f)
        {
            rotationAngle -= 360.0f;
        }
    }

    turbine(rotationAngle);
    drawSmoke();
    third_turbine();
    first_turbine();
    arman_tree();
    first_tree();
    second_tree();
    third_tree();
    arman_cloud();
    arman_cloud2();

    glFlush();


}


///////////////////////////////////////////////////////////prova////////










void s2_mouseCallback(int button, int state, int x, int y) {
        if (button == GLUT_LEFT_BUTTON && state == GLUT_DOWN) {
        s2_isMoving = false;
        std::cout << "Movement Stopped!" << std::endl;
    }
    else if (button == GLUT_RIGHT_BUTTON && state == GLUT_DOWN) {
        s2_isMoving = true;
        std::cout << "Movement Resumed!" << std::endl;
    }
}

void s2_triggerAction() {
    if (!s2_isActionTriggered) {
        s2_isActionTriggered = true;
        std::cout << "Action triggered (e.g., jump, fly a kite)" << std::endl;
        s2_actionTimer = 1.0f;

    }
}

void s2_keyboard(unsigned char key, int x, int y) {
    switch (key) {

        case 'd':  //day
            s2_isDay = true;
            std::cout << "Switched to Day Mode" << std::endl;
            break;

        case 'n':  //night
            s2_isDay = false;
            std::cout << "Switched to Night Mode" << std::endl;
            break;

        case 'r':
            // Reset all object positions to their initial state
            s2_boatX = 0.0f;
            s2_duckX = 0.0f;
            s2_cloud1X = 2.42f;
            s2_cloud2X = 6.42f;
            s2_cloud3X = 10.42f;
            s2_cloud4X = 14.42f;
            s2_cloud5X = 18.42f;

            s2_bird1X = 29.22f;
            s2_bird2X = 23.488f;
            s2_manX = 19.4f;
            s2_isActionTriggered = false;
            std::cout << "Scene Reset!" << std::endl;
            break;

            case 'V':
            s2_triggerAction();
            break;


        case '1':  // Make cloud1 move faster
            s2_cloudSpeed += 1.0f;
            std::cout << "Cloud 1 speed increased!" << std::endl;
            break;


        case '2':  // Make cloud3 move slower
             s2_cloudSpeed -= 1.0f;
            std::cout << "Cloud 1 speed decreased!" << std::endl;
            break;


        default:
            break;
    }

    glutPostRedisplay();  // Redraw the scene
}

void s2_drawSky() {
    if (s2_isDay) {
        glColor3f(0.154f, 0.84f, 0.87f);  // Day Sky - Light Blue
    } else {
        glColor3f(0.0f, 0.0f, 0.2f);  // Night Sky - Dark Blue
    }

    glBegin(GL_POLYGON);
    glVertex2f(0.0f, 10.26f); //E5
    glVertex2f(30.0f, 10.33f); //Q7
    glVertex2f(30.0f, 15.0f); //B
    glVertex2f(0.0f, 15.0f); //C
    glEnd();
}

void s2_drawCircle(float x, float y, float radius) {
    glBegin(GL_POLYGON);
    for (int i = 0; i < 360; i++) {
        float theta = i * 3.14159f / 180;
        glVertex2f(radius * cos(theta) + x, radius * sin(theta) + y);
    }
    glEnd();
}

void s2_drawGirl(float x, float y) {

// Head
    glColor3f(1.0f, 0.8f, 0.6f);  // Light skin color
    s2_drawCircle(13.67f, 10.83f,0.21f);


// Body
    glColor3f(0.8f, 0.4f, 0.6f); // Blue shirt
    glBegin(GL_POLYGON);
    glVertex2f(13.54f, 10.67f);//010
    glVertex2f(13.35f, 10.0f);//p10
    glVertex2f(13.90f, 10.0f);//n11
    glVertex2f(13.799f, 10.66f);//l11
    glEnd();

    // Legs
    //Left

    glColor3f(0.0f, 0.0f, 0.0f);  // Black pants
    glBegin(GL_POLYGON);
    glVertex2f(13.44f, 10.0f);//o11
    glVertex2f(13.44f, 9.8f);  // p11
    glVertex2f(13.5f, 9.8f);//l13
    glVertex2f(13.5f, 10.0f);  // m13
    glEnd();

    //Right

    glColor3f(0.0f, 0.0f, 0.0f);  // Black pants
    glBegin(GL_POLYGON);
    glVertex2f(13.68f, 10.0f);//n13
    glVertex2f(13.68f, 9.8f);  // o13
    glVertex2f(13.74f, 9.8f);//p13
    glVertex2f(13.74f, 10.0f);  // q13
    glEnd();
}

void s2_drawDuck(float x, float y) {
    // Body
    glColor3f(1.0f, 0.8f, 0.1f); // Yellow
    s2_drawCircle(x, y, 0.3f);

    // Head
    s2_drawCircle(x + 0.36f, y + 0.22f, 0.2f);

    // Beak+
    glColor3f(1.0f, 0.5f, 0.0f); // Orange
    glBegin(GL_TRIANGLES);
    glVertex2f(x + 0.5f, y + 0.31f);//G11
    glVertex2f(x + 0.5f, y + 0.2f);//F11
    glVertex2f(x + 0.6f, y + 0.35f);//H11
    glEnd();

    // Eye
    glColor3f(0.0f, 0.0f, 0.0f); // Black
    s2_drawCircle(x + 0.37f, y + 0.33f, 0.03f);
}

void s2_drawLittlePond() {
    glColor3f(0.13f, 0.73f, 0.85f); // Blue
    glBegin(GL_POLYGON);
    glVertex2f(30.0f, 8.5f);
    glVertex2f(21.94f, 8.48f);
    glVertex2f(19.81f, 4.0f);
    glVertex2f(30.0f, 4.0f);
    glEnd();

    // Add ducks to the pond with dynamic movement
    s2_drawDuck(24.55f + s2_duckX, 6.82f);
    s2_drawDuck(23.1f + s2_duckX, 5.91f);
}


// Function to draw a boat
void s2_drawBoat(float x, float y) {
    glColor3f(0.4f, 0.2f, 0.0f); // Brown
    glBegin(GL_POLYGON);
    glVertex2f(x, y);                   // Tip of the boat (T13)
    glVertex2f(x - 0.7f, y + 0.2f);     // Left top corner (U13)
    glVertex2f(x - 0.3f, y - 0.3f);     // Bottom-left corner (V13)
    glVertex2f(x + 0.3f, y - 0.3f);     // Bottom-right corner (W13)
    glVertex2f(x + 0.7f, y + 0.2f);
    glVertex2f(x - 0.7f, y + 0.2f);
    glEnd();

    glColor3f(1.0f, 1.0f, 1.0f); // White
    glBegin(GL_TRIANGLES);
    glVertex2f(x, y + 1.0f);        // Top vertex (A14)
glVertex2f(x, y + 0.2f);        // Bottom-left vertex (Z13)
glVertex2f(x + 0.4f, y + 0.5f); // Bottom-right vertex (D14)

    glEnd();
}

void s2_drawRoad()
{
    //draw ROAD

    glColor3f(0.52f, 0.49f, 0.48f); //RED
    glBegin(GL_POLYGON);
    glVertex2f(0.0f, 2.0f);//D
    glVertex2f(0.0f, 0.0f);//k10
    glVertex2f(30.0f, 0.0f);//L10
    glVertex2f(30.0f, 2.0f);//E
    glEnd();
}

void s2_drawBucket()
{
    //draw balti

    glColor3f(0.52f, 0.49f, 0.48f); //RED
    glBegin(GL_POLYGON);
    glVertex2f(14.95f, 9.7f);//q11
    glVertex2f(15.1f, 9.2f);//s11
    glVertex2f(15.3f, 9.2f);//t11
    glVertex2f(15.45f, 9.7f);//r11
    glEnd();
}

// Function to draw the water drop using the given points
void drawWater(float x, float y) {
    glColor3f(0.0f, 0.5f, 1.0f);  // Blue water color
    glBegin(GL_POLYGON);

    // Bottom elliptical part of the water drop (from 180 to 360 degrees)
    for (int i = 180; i <= 360; i++) {
        float theta = i * 3.14159f / 180;  // Convert degrees to radians
        glVertex2f(0.05f * cos(theta) + x, 0.1f * sin(theta) + y);  // Elliptical shape for bottom part
    }

    // Tip of the water drop (sharp top)
    glVertex2f(x, y + 0.15f);  // The sharp tip of the water drop

    glEnd();
}


void s2_drawManWithBox(float x, float y) {
    // Box on head
    glColor3f(0.5f, 0.35f, 0.05f);  // Brown box
    glBegin(GL_POLYGON);
        glVertex2f(x - 0.8f, y + 1.2f);  // Top-left corner
        glVertex2f(x - 0.8f, y + 0.8f);  // Bottom-left corner
        glVertex2f(x + 0.8f, y + 0.8f);  // Bottom-right corner
        glVertex2f(x + 0.8f, y + 1.2f);  // Top-right corner
    glEnd();

    // Head
    glColor3f(1.0f, 0.8f, 0.6f);  // Skin color
    s2_drawCircle(x, y+0.5, 0.27f); // Centered relative to (x, y)

// Body
glColor3f(0.0f, 0.0f, 1.0f);  // Blue shirt
glBegin(GL_POLYGON);
    glVertex2f(x - 0.14f + 0.05f, y + 0.21f);  // F5: (19.15, 9.87) moved slightly more right
    glVertex2f(x - 0.29f + 0.05f, y - 0.16f);  // R7: (19.0, 9.6) moved slightly more right
    glVertex2f(x - 0.29f + 0.05f, y - 0.56f);  // S7: (19.0, 9.2) moved slightly more right
    glVertex2f(x + 0.21f + 0.05f, y - 0.56f);  // U7: (19.5, 9.2) moved slightly more right
    glVertex2f(x + 0.21f + 0.05f, y - 0.16f);  // C8: (19.5, 9.6) moved slightly more right
    glVertex2f(x + 0.09f + 0.05f, y + 0.21f);  // T7: (19.33, 9.87) moved slightly more right
glEnd();

// LEFT
glColor3f(0.0f, 0.0f, 0.0f);  // Black pants
glBegin(GL_POLYGON);
    glVertex2f(x - 0.1f, y - 0.93f + 0.4f);  // V7: (19.1, 9.2) moved up
    glVertex2f(x - 0.1f, y - 1.13f + 0.4f);  // W7: (19.1, 9.0) moved up
    glVertex2f(x - 0.05f, y - 1.13f + 0.4f);  // K8: (19.15, 9.0) moved up
    glVertex2f(x - 0.05f, y - 0.93f + 0.4f);  // A8: (19.15, 9.2) moved up
glEnd();


// RIGHT - moved very up again
glBegin(GL_POLYGON);
    glVertex2f(x + 0.1f, y - 0.93f + 0.4f);  // M8: (19.3, 9.2) moved very up
    glVertex2f(x + 0.1f, y - 1.13f + 0.4f);  // N8: (19.3, 9.0) moved very up
    glVertex2f(x + 0.15f, y - 1.13f + 0.4f); // P8: (19.35, 9.0) moved very up
    glVertex2f(x + 0.15f, y - 0.93f + 0.4f); // O8: (19.35, 9.2) moved very up
glEnd();



    // Arms

  // Move a little bit more left by subtracting 0.4 from x
glBegin(GL_POLYGON);
glColor3f(0.0f, 0.0f, 1.0f);  // Blue shirt
    glVertex2f(x - 0.4f + 0.5f, y + 0.6f + 0.2f);  // Adjusted for further left shift and upward move
    glVertex2f(x - 0.4f + 0.7f, y + 0.2f + 0.2f);
    glVertex2f(x - 0.4f + 0.6f, y + 0.2f + 0.2f);
    glVertex2f(x - 0.4f + 0.4f, y + 0.6f + 0.2f);
glEnd();

glBegin(GL_POLYGON);
    glColor3f(0.0f, 0.0f, 1.0f);  // Blue shirt
    glVertex2f(x - 0.4f + 0.7f, y + 0.2f + 0.2f);  // B8: (19.5, 10.53)
    glVertex2f(x - 0.4f + 0.4f, y - 0.1f + 0.2f);  // F8: (19.2, 10.23)
    glVertex2f(x - 0.4f + 0.4f, y + 0.0f + 0.2f);  // L8: (19.2, 10.33)
    glVertex2f(x - 0.4f + 0.6f, y + 0.2f + 0.2f);  // D8: (19.4, 10.53)
glEnd();


    }


void s2_drawManScene() {
  /*  // You can modify the position (x, y) to place the man wherever you want
    float x = 19.4f; // Example: center of the scene
    float y = 9.6f; // Example: center of the scene

    // Draw the man at the specified position
    drawManWithBox(x, y);  */

    s2_drawManWithBox(s2_manX, 9.6f);
}

// Function to draw a simple boy
void s2_drawBoy(float x, float y)
{
    // Only draw the boy if it's daytime
    if (!s2_isDay) {
        return;  // Don't draw the boy at night
    }
    // Head
    glColor3f(1.0f, 0.8f, 0.6f);  // Skin color
    s2_drawCircle(9.80, 9.88f, 0.24f);

    // Body
    glColor3f(0.0f, 1.0f, 1.0f);  // Blue shirt
    glBegin(GL_POLYGON);
    glVertex2f(9.498f, 9.631f);//z9
    glVertex2f(9.65f, 9.0f);//c10
    glVertex2f(9.95f, 9.0f);//d10
    glVertex2f(10.12f, 9.63f);//a10
    glEnd();

    // Legs

    //left
    glColor3f(0.0f, 0.0f, 0.0f);  // Black pants
    glBegin(GL_POLYGON);
    glVertex2f(9.68f, 9.0f);//Q8
    glVertex2f(9.68f, 8.88f);//R8
    glVertex2f(9.72f, 8.88f);//S8
    glVertex2f(9.72f, 9.0f);//T8
    glEnd();

    //RIGHT
    glColor3f(0.0f, 0.0f, 0.0f);  // Black pants
    glBegin(GL_POLYGON);
    glVertex2f(9.84f, 9.0f);//D9
    glVertex2f(9.84f, 8.88f);//W9
    glVertex2f(9.88f, 8.88f);//I10
    glVertex2f(9.88f, 9.0f);//J9
    glEnd();

}

// Function to draw a kite (diamond shape) with slightly reduced size
void s2_drawKite(float x, float y) {
    // Only draw the kite if it's daytime
    if (!s2_isDay) {
        return;  // Don't draw the kite at night
    }

    float scale = 1.2f;  // Reduced scale factor to slightly decrease the kite size

    // Calculate the scaled coordinates relative to (x, y)
    float f10X = x + (10.70f - x) * scale;
    float f10Y = y + (13.66f - y) * scale;
    float e10X = x + (10.92f - x) * scale;
    float e10Y = y + (13.01f - y) * scale;
    float h10X = x + (11.57f - x) * scale;
    float h10Y = y + (13.40f - y) * scale;
    float g10X = x + (11.33f - x) * scale;
    float g10Y = y + (14.01f - y) * scale;

    glColor3f(1.0f, 0.0f, 0.0f);  // Red (for the kite)
    glBegin(GL_POLYGON);
        glVertex2f(f10X, f10Y);  // F10 (scaled)
        glVertex2f(e10X, e10Y);  // E10 (scaled)
        glVertex2f(h10X, h10Y);  // H10 (scaled)
        glVertex2f(g10X, g10Y);  // G10 (scaled)
    glEnd();

    // Kite string (black line) - Parameterized based on kite position
    glColor3f(0.0f, 0.0f, 0.0f);  // Black for the string
    glBegin(GL_LINES);
        glVertex2f(e10X, e10Y);  // Base of the kite (E10)
        glVertex2f(9.8f, 9.64f);  // String endpoint (parameterized relative to kite's position)
    glEnd();
}

void s2_drawKiteScene() {
    float kiteDynamicY = s2_kiteY; // Default to last known position

    // Only update Y position if movement is enabled
    if (s2_isDay) {
        if (s2_isMoving) {
            kiteDynamicY = s2_kiteY + s2_kiteAmplitude * sin(s2_kiteFrequency * glutGet(GLUT_ELAPSED_TIME));
        }
        s2_drawKite(s2_kiteX, kiteDynamicY); // Always draw the kite
    }
}

// Function to draw the tube well with animated handle
void s2_drawTubeWell(float x, float y) {
    // Draw the tube well's base
    glColor3f(0.5f, 0.5f, 0.5f);  // Gray
    glBegin(GL_POLYGON);
    glVertex2f(14.1f, 9.6f);//C4
    glVertex2f(14.1f, 9.2f);//A4
    glVertex2f(14.6f, 9.2f);//B4
    glVertex2f(14.6f, 9.6f);//D4
    glEnd();

    glColor3f(0.5f, 0.5f, 0.5f);  // Gray
    glBegin(GL_POLYGON);
    glVertex2f(14.27f, 9.6f);//E4
    glVertex2f(14.47f, 9.6f);//H4
    glVertex2f(14.47f, 9.8f);//G4
    glVertex2f(14.27f, 9.8f);//F4
    glEnd();

    glColor3f(0.5f, 0.5f, 0.5f);  // Gray
    glBegin(GL_POLYGON);
    glVertex2f(14.2f, 10.3f);//Q4
    glVertex2f(14.2f, 9.8f);//I4
    glVertex2f(14.55f, 9.8f);//J4
    glVertex2f(14.55f, 10.3f);//R4
    glEnd();

    // Draw the tube
    glColor3f(0.5f, 0.5f, 0.5f);  // Gray
    glBegin(GL_POLYGON);
    glVertex2f(14.55f, 10.2f);//p4
    glVertex2f(14.55f, 10.05f);//t4
    glVertex2f(15.2f, 10.05f);//u4
    glVertex2f(15.2f, 10.2f);//s4
    glEnd();


// Draw the handle with rotation (simulating movement)
    glColor3f(0.0f, 0.0f, 0.0f);  // Dark Gray
    glPushMatrix();
    glTranslatef(14.3f, 10.3f, 0.0f);  // Move pivot slightly right (from 14.2 to 14.3)
    glRotatef(s2_handleAngle, 0.0f, 0.0f, 1.0f);  // Rotate handle based on handleAngle
    glBegin(GL_LINES);
    glVertex2f(0.0f, 0.0f);  // Starting point (pivot)
    glVertex2f(-0.5f, 0.3f);  // Ending point (rotated part of the handle)
    glEnd();
    glPopMatrix();

}

// Function to draw the tube well area and animate it
void s2_drawTubeWellArea() {
    // Draw the tube well area (base polygon)
    glColor3f(0.59f, 0.29f, 0.05f);  // Dark brown color
    glBegin(GL_POLYGON);
    glVertex2f(11.83f, 8.38f);  // U3
    glVertex2f(15.91f, 8.39f);  // V3
    glVertex2f(17.01f, 10.10f);  // Z3
    glVertex2f(13.27f, 10.09f);  // W3
    glEnd();

    // Draw the tube well with moving handle
    s2_drawTubeWell(13.5f, 8.5f);

    // Draw the water flowing from the tube well
    drawWater(15.2f, s2_waterY);
}


void s2_drawSingleCloud(float x, float y) {
    glColor3f(1.0f, 1.0f, 1.0f);

    for (int i = 0; i < 3; i++) {
        glBegin(GL_POLYGON);
        for (int j = 0; j < 360; j++) {
            float theta = j * 3.14159f / 180;
            float radius = (i == 1) ? 0.7f : (i == 2 ? 0.5f : 0.5f);
            glVertex2f(radius * cos(theta) + x + i * 0.75f, radius * sin(theta) + y);
        }
        glEnd();
    }
}

void s2_drawClouds()
    {
        s2_drawSingleCloud(s2_cloud1X, s2_cloud1Y);
        s2_drawSingleCloud(s2_cloud2X, s2_cloud2Y);
        s2_drawSingleCloud(s2_cloud3X, s2_cloud3Y);
        s2_drawSingleCloud(s2_cloud4X, s2_cloud4Y);
        s2_drawSingleCloud(s2_cloud5X, s2_cloud5Y);
    }

void s2_drawSun() {
    if (s2_isDay) {
        glColor3f(1.0f, 1.0f, 0.0f);  // Bright Yellow for the Sun
    } else {
        glColor3f(1.0f, 1.0f, 1.0f);  // White for the Moon
    }
    s2_drawCircle(27.2f, 13.39f, 1.3f);  // Position remains same

}

void s2_drawBird(float x, float y) {
    glColor3f(0.0f, 0.0f, 0.0f); // Set the bird color to black

    // Calculate the flapping effect using sine wave
    float wingFlapOffset = sin(s2_wingFlap) * 0.2f;  // Flap the wings up and down
    // Draw the left wing using triangle
    glBegin(GL_TRIANGLES);
    glVertex2f(x - 0.01f, y); // Left wing base point (q1)
    glVertex2f(x - 0.21f, y + 0.35f + wingFlapOffset); // Top-left wing point with flap (real position: 29.02, 12.88)
    glVertex2f(x - 1.03f, y); // Left wing tip point (m15)
    glEnd();


    // Draw the right wing using triangle
    glBegin(GL_TRIANGLES);
    glVertex2f(x - 1.0f, y); // q1 moved further left by 1.0f
    glVertex2f(x + 0.09f + 0.3f, y + 0.31f + wingFlapOffset); // N15: Move N15 to the right
    glVertex2f(x + 1.27f - 0.8f, y); // P15: Move P15 further left
    glEnd();


}


void S2_drawBirds() {
    s2_drawBird(s2_bird1X, s2_bird1Y); // Bird 1
    s2_drawBird(s2_bird2X, s2_bird2Y); // Bird 2
}

// Function to draw semicircle bushes
void s2_drawBushes()
{
    glColor3f(0.0f, 0.8f, 0.0f); // Green

    glBegin(GL_POLYGON);
    //front base
    glVertex2f(0.0f, 10.26f);//E5
    glVertex2f(0.22f, 10.99f);//G5
    glVertex2f(0.91f, 11.22f);//K5
    glVertex2f(1.26f, 10.997f);//L5
    glVertex2f(2.0f, 11.5f);//M5
    glVertex2f(2.38f, 11.35f);//N5
    glVertex2f(2.92f, 11.57f);//O5
    glVertex2f(3.14f, 11.55f);//P5
    glVertex2f(3.85f, 11.75f);//S5
    glVertex2f(4.0f, 11.5f);//T5
    glVertex2f(4.51f, 11.40f);//U5
    glVertex2f(4.75f, 10.94f);//V5
    glVertex2f(5.096f, 10.797f);//W5
    glVertex2f(5.78f, 11.21f);//Z5
    glVertex2f(6.27f, 11.29f);//G13
    glVertex2f(6.58f, 11.92f);//H13
    glVertex2f(6.88f, 11.97f);//K13
    glVertex2f(6.98f, 11.80f);//A6
    glVertex2f(7.86f, 11.46f);//B6
    glVertex2f(8.41f, 11.28f);//C6
    glVertex2f(8.97f, 11.75f);//D6
    glVertex2f(9.83f, 11.63f);//E6
    glVertex2f(10.23f, 11.18f);//T6
    glVertex2f(10.93f, 11.65f);//V11
    glVertex2f(11.71f, 11.55f);//W11
    glVertex2f(12.31f, 10.88f);//Z11
    glVertex2f(12.85f, 10.99f);//A12
    glVertex2f(13.38f, 11.26f);//B12
    glVertex2f(14.03f, 11.56f);//C12
    glVertex2f(14.41f, 11.58f);//D12
    glVertex2f(14.96f, 11.33f);//E12
    glVertex2f(16.02f, 11.78f);//F12
    glVertex2f(16.88f, 11.13f);//G12
    glVertex2f(17.36f, 11.18f);//H12
    glVertex2f(18.12f, 11.73f);//K12
    glVertex2f(18.66f, 11.39f);//L12
    glVertex2f(19.28f, 11.85f);//M12
    glVertex2f(19.64f, 11.95f);//N12
    glVertex2f(19.86f, 11.78f);//O12
    glVertex2f(20.62f, 11.12f);//P12
    glVertex2f(21.11f, 11.37f);//Q12
    glVertex2f(22.11f, 12.02f);//R12
    glVertex2f(22.5f, 12.0f);//S12
    glVertex2f(22.89f, 11.58f);//T12
    glVertex2f(24.03f, 11.25f);//V12
    glVertex2f(24.73f, 11.82f);//W12
    glVertex2f(25.67f, 11.98f);//Z12
    glVertex2f(26.57f, 11.42f);//A13
    glVertex2f(27.03f, 11.28f);//B13
    glVertex2f(28.04f, 11.19f);//C13
    glVertex2f(28.71f, 11.72f);//D13
    glVertex2f(29.499f, 11.77f);//E13
    glVertex2f(29.68f, 11.57f);//F13
    glVertex2f(30.00f, 10.30f);//L13
    glEnd();
}

void s2_displayHouse2()
{
    // Draw the house base
    glColor3f(0.812f, 0.45f, 0.08f); // Brown
    glBegin(GL_POLYGON);
    //front base
    glVertex2f(4.2f, 6.2f);//L1
    glVertex2f(4.2f, 4.6f);//M1
    glVertex2f(6.8f, 4.6f);//N1
    glVertex2f(6.8f, 6.2f);//O1
    glEnd();

    glColor3f(0.8f, 0.5f, 0.2f); // Brown
    glBegin(GL_POLYGON);
    //back base
    glVertex2f(6.8f, 4.6f);//N1
    glVertex2f(7.8315f, 4.7964f);//p1
    glVertex2f(7.825f, 6.111f);//k1
    glVertex2f(7.313f, 6.854f);//j1
    glVertex2f(6.8f, 6.0f);//h1
    glEnd();

    // Draw the front roof
    glColor3f(0.5f, 0.1f, 0.1f); // Dark red
    glBegin(GL_POLYGON);
    glVertex2f(4.5f, 7.0f);//F1
    glVertex2f(3.8f, 6.0f);//G1
    glVertex2f(6.8f, 6.0f);//H1
    glVertex2f(7.4f, 7.0f);//E1
    glEnd();

    //  Draw the side roof
    glColor3f(0.5f, 0.1f, 0.1f); // Dark red
    glBegin(GL_POLYGON);
    glVertex2f(7.313f, 6.854f);//j1
    glVertex2f(7.825f, 6.111f);//k1
    glVertex2f(8.012f, 6.077f);//L1
    glVertex2f(7.4f, 7.0f);//E1
    glEnd();

    // Draw the door
    glColor3f(0.3f, 0.2f, 0.1f); // Dark brown
    glBegin(GL_POLYGON);
    glVertex2f(5.0f, 5.4f);//U1
    glVertex2f(5.0f, 4.6f);//T1
    glVertex2f(5.8f, 4.6f);//W1
    glVertex2f(5.8f, 5.4f);//V1
    glEnd();

    // Draw windows
    if (s2_isDay) {
    glColor3f(0.3f, 0.2f, 0.1f);  // Day - Dark Brown Windows
} else {
    glColor3f(1.0f, 1.0f, 0.0f);  // Night - Yellow Windows (Lit up)
}
    glBegin(GL_POLYGON);
    glVertex2f(7.1f, 5.7);//Z1
    glVertex2f(7.1f, 5.3f);//B2
    glVertex2f(7.5f, 5.3f);//C2
    glVertex2f(7.5f, 5.7f);//A2
    glEnd();

    //DRAW BIT
    glColor3f(0.5f, 0.1f, 0.1f); //RED
    glBegin(GL_POLYGON);
    glVertex2f(4.2f, 4.6f);//M1
    glVertex2f(4.0f, 4.4f);//Q1
    glVertex2f(6.95f, 4.39f);//R1
    glVertex2f(8.15f, 4.67f);//S1
    glVertex2f(7.832f, 4.796f);//P1
    glVertex2f(6.8f, 4.6f);//N1
    glEnd();


}

void s2_displayHouse3()
{
    glClear(GL_COLOR_BUFFER_BIT);


     // house
    glColor3f(0.812f, 0.498f, 0.165f); // Brown
    glBegin(GL_POLYGON);
    //front base
    glVertex2f(6.4f, 8.2f);//S10
    glVertex2f(6.4f, 6.8f);//D11
    glVertex2f(8.4f, 6.8f);//C11
    glVertex2f(8.4f, 8.2f);//T10
    glEnd();

    glColor3f(0.812f, 0.45f, 0.08f); // Brown
    glBegin(GL_POLYGON);
    //back base
    glVertex2f(5.596f, 8.24f);//W10
    glVertex2f(5.6f, 7.0f);//A11
    glVertex2f(8.4f, 6.8f);//D11
    glVertex2f(6.4f, 8.2f);//S10
    glVertex2f(6.06f, 8.87f);//V10
    glEnd();

    // front roof
    glColor3f(0.5f, 0.1f, 0.1f); // Dark red
    glBegin(GL_POLYGON);
    glVertex2f(6.0f, 9.0f);//Q10
    glVertex2f(6.4f, 8.2f);//S10
    glVertex2f(8.4f, 8.2f);//T10
    glVertex2f(7.8f, 9.0f);//R10
    glEnd();

    //side roof
    glColor3f(0.5f, 0.1f, 0.1f); // Dark red
    glBegin(GL_POLYGON);
    glVertex2f(6.0f, 9.0f);//Q10
    glVertex2f(5.37f, 8.17f);//Z10
    glVertex2f(5.596f, 8.24f);//W10
    glVertex2f(6.06f, 8.87f);//V10
    glEnd();

    // door
    glColor3f(0.3f, 0.2f, 0.1f); // Dark brown
    glBegin(GL_POLYGON);
    glVertex2f(7.0f, 7.6f);//Q14
    glVertex2f(7.0f, 6.8f);//P14
    glVertex2f(7.8f, 6.8f);//U14
    glVertex2f(7.8f, 7.6f);//T14
    glEnd();

     // windows
    if (s2_isDay) {
      glColor3f(0.3f, 0.2f, 0.1f);  // Day - Dark Brown Windows
    }
    else {
    glColor3f(1.0f, 1.0f, 0.0f);  // Night - Yellow Windows (Lit up)
    }

    glBegin(GL_POLYGON);
    glVertex2f(5.8f, 7.9f); // V14
    glVertex2f(5.8f, 7.5f); // A15
    glVertex2f(6.3f, 7.5f); // Z14
    glVertex2f(6.3f, 7.9f); // W14
    glEnd();

    //DRAW BIT
    glColor3f(0.5f, 0.1f, 0.1f); //RED
    glBegin(GL_POLYGON);
    glVertex2f(7.53f, 6.8f);//014
    glVertex2f(7.60f, 6.69f);//N14
    glVertex2f(8.53f, 6.71f);//B11
    glVertex2f(8.4f, 6.8f);//C11
    glEnd();

}


void s2_displayHouse4()
{
     //  house base
    glColor3f(0.812f, 0.45f, 0.08f); // Brown
    glBegin(GL_POLYGON);
    //front base
    glVertex2f(9.0f, 6.6f);//M
    glVertex2f(9.0f, 5.0f);//O
    glVertex2f(11.4f, 5.0f);//P
    glVertex2f(11.4f, 6.6f);//N
    glEnd();

    glColor3f(0.8f, 0.5f, 0.2f); // Brown
    glBegin(GL_POLYGON);
    //back base
    glVertex2f(11.4f, 6.53f);//B15
    glVertex2f(11.4f, 5.0f);//P
    glVertex2f(12.54f, 5.20f);//Q
    glVertex2f(12.54f, 6.7f);//L
    glVertex2f(12.09f, 7.35f);//K
    glEnd();

    // front roof
    glColor3f(0.5f, 0.1f, 0.1f); // Dark red
    glBegin(GL_POLYGON);
    glVertex2f(9.5f, 7.5f);//F
    glVertex2f(8.5f, 6.5f);//H
    glVertex2f(11.38f, 6.5f);//I
    glVertex2f(12.197f, 7.5f);//G
    glEnd();

    // side roof
    glColor3f(0.5f, 0.1f, 0.1f); // Dark red
    glBegin(GL_POLYGON);
    glVertex2f(12.09f, 7.35f);//K
    glVertex2f(12.54f, 6.7f);//L
    glVertex2f(12.81f, 6.5f);//J
    glVertex2f(12.197f, 7.48f);//G
    glEnd();

    // door
    glColor3f(0.3f, 0.2f, 0.1f); // Dark brown
    glBegin(GL_POLYGON);
    glVertex2f(9.8f, 5.8f);//V
    glVertex2f(9.8f, 5.0f);//U
    glVertex2f(10.5f, 5.0f);//Z
    glVertex2f(10.5f, 5.8f);//W
    glEnd();

    // windows
    if (s2_isDay) {
    glColor3f(0.3f, 0.2f, 0.1f);  // Day - Dark Brown Windows
    }
    else {
    glColor3f(1.0f, 1.0f, 0.0f);  // Night - Yellow Windows
    }
    glBegin(GL_POLYGON);
    glVertex2f(11.8f, 6.2f);//A1
    glVertex2f(11.8f, 5.8f);//D1
    glVertex2f(12.2f, 5.8f);//C1
    glVertex2f(12.2f, 6.2f);//B1
    glEnd();


    //DRAW BIT
    glColor3f(0.5f, 0.1f, 0.1f); //RED
    glBegin(GL_POLYGON);
    glVertex2f(9.0f, 5.0f);//O
    glVertex2f(8.8f, 4.8f);//R
    glVertex2f(11.5f, 4.8f);//S
    glVertex2f(11.4f, 5.0f);//P
    glEnd();

    //DRAW BIT 2
    glColor3f(0.5f, 0.1f, 0.1f); //RED
    glBegin(GL_POLYGON);
    glVertex2f(11.4f, 5.0f);//P
    glVertex2f(11.5f, 4.8f);//S
    glVertex2f(12.78f, 5.11f);//T
    glVertex2f(12.54f, 5.20f);//Q
    glEnd();


}

void s2_displayHouse5()
{
  // house base
    glColor3f(0.89f, 0.5f, 0.09f); // Brown
    glBegin(GL_POLYGON);
    //front base
    glVertex2f(16.6f, 6.8f);//G2
    glVertex2f(16.6f, 5.6f);//O2
    glVertex2f(19.4f, 5.6f);//P2
    glVertex2f(19.4f, 6.8f);//H2
    glEnd();

    glColor3f(0.812f, 0.45f, 0.08f); // Brown
    glBegin(GL_POLYGON);
    //back base

    glVertex2f(16.25f, 7.85f);//I2
    glVertex2f(15.5998f, 6.844f);//J2
    glVertex2f(15.596f, 5.73f);//Q2
    glVertex2f(16.6f, 5.6f);//O2
    glVertex2f(16.6f, 6.8f);//G2
    glEnd();

    //front roof
    glColor3f(0.5f, 0.1f, 0.1f); // Dark red
    glBegin(GL_POLYGON);
    glVertex2f(16.2f, 8.0f);//D2
    glVertex2f(16.6f, 6.8f);//G2
    glVertex2f(19.4f, 6.8f);//H2
    glVertex2f(19.0f, 8.0f);//E2
    glEnd();

    //side roof
    glColor3f(0.5f, 0.1f, 0.1f); // Dark red
    glBegin(GL_POLYGON);
    glVertex2f(16.2f, 8.0f);//D2
    glVertex2f(15.4f, 6.8f);//F2
    glVertex2f(15.5998f, 6.844f);//J2
    glVertex2f(16.25f, 7.85f);//I2
    glEnd();

    //door
    glColor3f(0.3f, 0.2f, 0.1f); // Dark brown
    glBegin(GL_POLYGON);
    glVertex2f(15.82f, 6.4f);//I3
    glVertex2f(15.82f, 5.698f);//H3
    glVertex2f(16.199f, 5.66f);//K3
    glVertex2f(16.2f, 6.4f);//J3
    glEnd();

    //  windows
    //window 1
    if (s2_isDay) {
    glColor3f(0.3f, 0.2f, 0.1f);  // Day - Dark Brown Windows
    }
    else {
    glColor3f(1.0f, 1.0f, 0.0f);  // Night - Yellow Windows (Lit up)
    }
    glBegin(GL_POLYGON);
    glVertex2f(17.0f, 6.4f);//M3
    glVertex2f(17.01f, 5.92f);//P3
    glVertex2f(17.71f, 5.92f);//O3
    glVertex2f(17.71f, 6.4f);//N3
    glEnd();

    //window 2
    if (s2_isDay) {
    glColor3f(0.3f, 0.2f, 0.1f);  // Day - Dark Brown Windows
    }
    else {
    glColor3f(1.0f, 1.0f, 0.0f);  // Night - Yellow Windows
    }
    glBegin(GL_POLYGON);
    glVertex2f(18.2f, 6.4f);//Q3
    glVertex2f(18.20f, 5.92f);//R3
    glVertex2f(18.80f, 5.92f);//S3
    glVertex2f(18.8f, 6.4f);//T3
    glEnd();

   //DRAW BIT
    glColor3f(0.5f, 0.1f, 0.1f); //RED
    glBegin(GL_POLYGON);
    glVertex2f(16.6f, 5.6f);//O2
    glVertex2f(16.3f, 5.42f);//L3
    glVertex2f(16.6f, 5.37f);//S2
    glEnd();

    //DRAW BIT 2
    glColor3f(0.5f, 0.1f, 0.1f); //RED
    glBegin(GL_POLYGON);
    glVertex2f(16.6f, 5.6f);//O2
    glVertex2f(16.6f, 5.37f);//S2
    glVertex2f(19.696f, 5.39f);//T2
    glVertex2f(19.4f, 5.6f);//P2
    glEnd();

    //DRAW CHAD
    glColor3f(0.5f, 0.1f, 0.1f); //RED
    glBegin(GL_POLYGON);
    glVertex2f(15.9f, 7.1f);//K2
    glVertex2f(15.29f, 6.61f);//M2
    glVertex2f(15.8f, 6.6f);//N2
    glVertex2f(16.33f, 7.098f);//L2
    glEnd();

    //DRAW LINE 1
    glColor3f(0.0f, 0.0f, 0.0f); //RED
    glBegin(GL_POLYGON);
    glVertex2f(15.29f, 6.61f);//M2
    glVertex2f(15.31f, 5.48f);//C15
    glVertex2f(15.29f, 5.49f);//R2
    glVertex2f(15.31f, 6.61f);//D15
    glEnd();


     //DRAW LINE 2
    glColor3f(0.0f, 0.0f, 0.0f); //RED
    glBegin(GL_POLYGON);
    glVertex2f(15.79f, 6.60f);//F15
    glVertex2f(15.78f, 5.40f);//E15
    glVertex2f(15.8f, 5.4f);//U2
    glVertex2f(15.8f, 6.6f);//N2

    glEnd();

     //DRAW SHIRI1
    glColor3f(0.42f, 0.55f, 0.6f); //RED
    glBegin(GL_POLYGON);
    glVertex2f(15.596f, 5.73f);//Q2
    glVertex2f(15.05f, 5.48f);//V2
    glVertex2f(16.07f, 5.27f);//W2
    glVertex2f(16.6f, 5.6f);//O2
    glEnd();

    //DRAW SHIRI2
    glColor3f(0.0f, 0.0f, 0.0f); //RED
    glBegin(GL_POLYGON);
    glVertex2f(15.05f, 5.48f);//V2
    glVertex2f(15.03f, 5.46f);//D3
    glVertex2f(16.08f, 5.24f);//E3
    glVertex2f(16.07f, 5.27f);//W2
    glEnd();

      //DRAW SHIRI3
    glColor3f(0.42f, 0.55f, 0.6f); //RED
    glBegin(GL_POLYGON);
    glVertex2f(15.03f, 5.46f);//D3
    glVertex2f(14.92f, 5.33f);//Z2
    glVertex2f(16.13f, 5.05f);//A3
    glVertex2f(16.08f, 5.24f);//E3
    glEnd();

     //DRAW SHIRI4
    glColor3f(0.0f, 0.0f, 0.0f); //RED
    glBegin(GL_POLYGON);
    glVertex2f(14.92f, 5.33f);//Z2
    glVertex2f(14.897f, 5.30f);//F3
    glVertex2f(16.14f, 5.02f);//G3
    glVertex2f(16.13f, 5.05f);//A3
    glEnd();

}

void s2_displayTree1()
{

    // house base
    glColor3f(0.47f, 0.38f, 0.21f); // Brown
    glBegin(GL_POLYGON);
    glVertex2f(13.5f, 6.6f);//G9
    glVertex2f(13.6f, 5.2f);//C9
    glVertex2f(13.9f, 5.2f);//N9
    glVertex2f(13.9f, 6.6f);//O9
    glEnd();

    glColor3f(0.388f, 0.58f, 0.196f); // Brown
    glBegin(GL_TRIANGLES);
    //UPPER
    glVertex2f(13.78f, 7.96f);//R9
    glVertex2f(12.98f, 6.26f);//Q9
    glVertex2f(14.5f, 6.3f);//S9
    glEnd();

    glColor3f(0.388f, 0.58f, 0.196f); // Brown
    glBegin(GL_TRIANGLES);
    //UPPER
    glVertex2f(13.8f, 8.2f);//R17
    glVertex2f(12.8f, 6.6f);//O17
    glVertex2f(14.8f, 6.6f);//S17
    glEnd();

    glColor3f(0.388f, 0.58f, 0.196f); // Brown
    glBegin(GL_TRIANGLES);
    //UPPER
    glVertex2f(13.8f, 8.3f);//W17
    glVertex2f(12.9f, 7.2f);//V17
    glVertex2f(14.7f, 7.2f);//Z17
    glEnd();
}

void s2_displayTree2()
{
    // Draw the house base
    glColor3f(0.47f, 0.38f, 0.21f); // Brown
    glBegin(GL_POLYGON);
    //front base
    glVertex2f(2.0f, 7.2f);//v8
    glVertex2f(2.0f, 5.0f);//u8
    glVertex2f(2.6f, 5.0f);//w8
    glVertex2f(2.6f, 7.2f);//z8
    glEnd();

    // Head
    glColor3f(0.29f, 0.55f, 0.03f); // Brown
    s2_drawCircle(2.31f, 8.00f,0.9f);//A9
    s2_drawCircle(1.24f, 8.187f,0.7f);//E9
    s2_drawCircle(3.11f, 8.097f,0.6f);//H9
    s2_drawCircle(2.99f, 9.28f,0.7f);//J9
    s2_drawCircle(1.32f, 9.41f,0.95f);//L9
    s2_drawCircle(2.29f, 9.79f, 0.93f);//T15
}

void s2_displayTree3()
{
    //house base
    glColor3f(0.47f, 0.38f, 0.21f); // Brown
    glBegin(GL_POLYGON);
    //Single trunk
    glVertex2f(5.0f, 10.2f);//N6
    glVertex2f(5.0f, 8.0f);//O6
    glVertex2f(5.6f, 8.0f);//L6
    glVertex2f(5.6f, 10.2f);//M6
    glEnd();

    // Head
    glColor3f(0.29f, 0.55f, 0.03f); // Brown
    s2_drawCircle(5.16f, 10.77f,0.9f);//W5
    s2_drawCircle(4.47f, 10.92f,0.6f);//Q6
    s2_drawCircle(5.81f, 10.94f,0.5f);//V15
    s2_drawCircle(5.17f, 11.72f,0.6f);//Z15
}


void s2_displayTree4()
{

    glColor3f(0.47f, 0.38f, 0.21f); // Brown
    glBegin(GL_POLYGON);
    //Single trunk
    glVertex2f(20.3f, 12.0f);//D16
    glVertex2f(20.3f, 9.8f);//B16
    glVertex2f(20.7f, 9.8f);//C16
    glVertex2f(20.7f, 12.0f);//E16
    glEnd();

    // Head
    glColor3f(0.29f, 0.55f, 0.03f); // Brown
    s2_drawCircle(20.53f, 12.26f,0.5f);//R6
    s2_drawCircle(20.11f, 12.45f,0.4f);//U6
    s2_drawCircle(20.87f, 12.52f,0.3f);//W6
    s2_drawCircle(20.47f, 12.90f,0.3f);//A7
}


void s2_displayTree5()
{

    glColor3f(0.47f, 0.38f, 0.21f); // Brown
    glBegin(GL_POLYGON);
    //Single trunk
    glVertex2f(24.0f, 12.0f);//E7
    glVertex2f(24.0f, 9.8f);//C7
    glVertex2f(24.4f, 9.8f);//D7
    glVertex2f(24.4f, 12.0f);//F7
    glEnd();

    // Head
    glColor3f(0.29f, 0.55f, 0.03f);
    glBegin(GL_POLYGON);
    glVertex2f(24.2f, 12.3f);//I7
    glVertex2f(23.5f, 11.7f);//H7
    glVertex2f(24.9f, 11.7f);//J7
    glEnd();

    glColor3f(0.29f, 0.55f, 0.03f);
    glBegin(GL_POLYGON);
    //front base
    glVertex2f(24.2f, 12.4f);//S16
    glVertex2f(23.5f, 11.9f);//R16
    glVertex2f(24.9f, 11.9f);//T16
    glEnd();


    glColor3f(0.29f, 0.55f, 0.03f);
    glBegin(GL_POLYGON);
    //front base
    glVertex2f(24.2f, 12.6f);//Z16
    glVertex2f(23.5f, 12.1f);//W16
    glVertex2f(24.9f, 12.1f);//A17
    glEnd();

    glColor3f(0.29f, 0.55f, 0.03f);
    glBegin(GL_POLYGON);
    //front base
    glVertex2f(24.2f, 13.0f);//E17
    glVertex2f(23.6f, 12.4f);//D17
    glVertex2f(24.8f, 12.4f);//F17
    glEnd();


}

void s2_displayTree6()
{
    //Single trunk
    glColor3f(0.47f, 0.38f, 0.21f);
    glBegin(GL_POLYGON);
    glVertex2f(7.4f, 12.2f);//D18
    glVertex2f(7.4f, 9.8f);//B18
    glVertex2f(7.8f, 9.8f);//C18
    glVertex2f(7.8f, 12.2f);//E18
    glEnd();

    // head
    glColor3f(0.29f, 0.55f, 0.03f); // Brown
    s2_drawCircle(7.6f, 12.4f,0.4f);//F18
    s2_drawCircle(7.3f, 12.4f,0.25f);//J18
    s2_drawCircle(7.911f, 12.48f,0.25f);//R18
    s2_drawCircle(7.6f, 12.8f,0.32f);//T18
}

void s2_displayTree7()
{
    //Single trunk
    glColor3f(0.47f, 0.38f, 0.21f); // Brown
    glBegin(GL_POLYGON);
    glVertex2f(11.2f, 12.2f);//m19
    glVertex2f(11.2f, 9.8f);//i19
    glVertex2f(11.6f, 9.8f);//j19
    glVertex2f(11.6f, 12.2f);//o19
    glEnd();

    // Head
    glColor3f(0.29f, 0.55f, 0.03f);
    glBegin(GL_POLYGON);
    glVertex2f(11.48f, 12.65f);//R19
    glVertex2f(10.74f, 11.73f);//Q19
    glVertex2f(12.12f, 11.74f);//S19
    glEnd();

    glColor3f(0.29f, 0.55f, 0.03f);
    glBegin(GL_POLYGON);
    //front base
    glVertex2f(24.2f, 12.4f);//S16
    glVertex2f(23.5f, 11.9f);//R16
    glVertex2f(24.9f, 11.9f);//T16
    glEnd();


    glColor3f(0.29f, 0.55f, 0.03f); // Brown
    glBegin(GL_POLYGON);
    //front base
    glVertex2f(11.45f, 12.83f);//W19
    glVertex2f(10.595f, 11.96f);//V19
    glVertex2f(12.26f, 12.07f);//Z19
    glEnd();

    glColor3f(0.29f, 0.55f, 0.03f); // Brown
    glBegin(GL_POLYGON);
    //front base
    glVertex2f(11.44f, 13.03f);//D20
    glVertex2f(10.53f, 12.36f);//C20
    glVertex2f(12.4f, 12.4f);//E20
    glEnd();


}

void s2_displayTree8()
{
    //Single trunk
    glColor3f(0.47f, 0.38f, 0.21f); // Brown
    glBegin(GL_POLYGON);
    glVertex2f(27.8f, 11.2f);//K20
    glVertex2f(27.8f, 9.8f);//I20
    glVertex2f(28.2f, 9.8f);//J20
    glVertex2f(28.2f, 11.2f);//L20
    glEnd();

    //head
    glColor3f(0.29f, 0.55f, 0.03f); // Brown
    s2_drawCircle(27.99f, 11.58f,0.47f);//M20
    s2_drawCircle(28.28f, 11.95f,0.38f);//Q20
    s2_drawCircle(27.58f, 11.55f,0.40f);//P20
    s2_drawCircle(27.74f, 12.19f,0.41f);//S20
}


void s2_displayBorder()
{
    // border_1
    glColor3f(0.47f, 0.38f, 0.21f); // Brown
    glBegin(GL_POLYGON);
    glVertex2f(21.8f, 8.8f);//I6
    glVertex2f(21.94f, 8.48f);//L7
    glVertex2f(30.0f, 8.5f);//K7
    glVertex2f(30.0f, 8.8f);//H6
    glEnd();

    //border_2
    glColor3f(0.47f, 0.38f, 0.21f); // Brown
    glBegin(GL_POLYGON);
    glVertex2f(21.8f, 8.8f);//I6
    glVertex2f(19.4f, 3.8f);//J6
    glVertex2f(19.81f,4.01f);//M7
    glVertex2f(21.94f, 8.48f);//L7
    glEnd();

    //border_3
    glColor3f(0.47f, 0.38f, 0.21f); // Brown
    glBegin(GL_POLYGON);
    glVertex2f(19.81f,4.01f);//M7
    glVertex2f(19.4f, 3.8f);//J6
    glVertex2f(30.0f, 3.8f);//K6
    glVertex2f(30.0f, 4.0f);//N7
    glEnd();

    //border_4
    glColor3f(0.47f, 0.38f, 0.21f); // Brown
    glBegin(GL_POLYGON);
    glVertex2f(13.2f,10.2f);//u20
    glVertex2f(11.61f, 8.296f);//V20
    glVertex2f(11.83f, 8.38f);//U3
    glVertex2f(13.27f, 10.09f);//W3
    glEnd();

    //border_5
    glColor3f(0.47f, 0.38f, 0.21f); // Brown
    glBegin(GL_POLYGON);
    glVertex2f(11.61f, 8.296f);//V20
    glVertex2f(16.07f, 8.31f);//W20
    glVertex2f(15.91f, 8.39f);//V3
    glVertex2f(11.83f, 8.38f);//U3
    glEnd();

    //border_6
    glColor3f(0.47f, 0.38f, 0.21f); // Brown
    glBegin(GL_POLYGON);
    glVertex2f(15.91f, 8.39f);//V3
    glVertex2f(16.07f, 8.31f);//W20
    glVertex2f(17.23f, 10.19f);//Z20
    glVertex2f(17.01f, 10.10f);//Z3
    glEnd();

    //border_7
    glColor3f(0.47f, 0.38f, 0.21f); // Brown
    glBegin(GL_POLYGON);
    glVertex2f(13.2f,10.2f);//u20
    glVertex2f(13.27f, 10.09f);//W3
    glVertex2f(17.01f, 10.10f);//Z3
    glVertex2f(17.23f, 10.19f);//Z20
    glEnd();

}

void s2_displayBracket()
{
    //1
    glColor3f(0.59f, 0.29f, 0.05f);
    glBegin(GL_POLYGON);
    glVertex2f(0.0f, 3.8f);//L10
    glVertex2f(0.0f, 3.71f);//C21
    glVertex2f(6.21f, 3.71f);//D21
    glVertex2f(6.2f, 3.8f);//H6
    glEnd();

    //2
    glBegin(GL_POLYGON);
    glVertex2f(0.0f, 3.12f);//Q22
    glVertex2f(0.0f, 2.92f);//Q22
    glVertex2f(6.16f, 2.9f);//S22
    glVertex2f(6.16f, 3.14f);//R22
    glEnd();

    //3
    glBegin(GL_POLYGON);
    glVertex2f(0.0f, 2.18f);//U22
    glVertex2f(0.0f, 2.0f);//D
    glVertex2f(6.17f, 2.0f);//W22
    glVertex2f(6.16f, 2.28f);//R22
    glEnd();

    //4
    glBegin(GL_POLYGON);
    glVertex2f(0.196f, 3.91f);//F21
    glVertex2f(0.2f, 2.0f);//E21
    glVertex2f(0.4f, 2.0f);//H21
    glVertex2f(0.395f, 3.91f);//G21
    glEnd();

    //5
    glBegin(GL_POLYGON);
    glVertex2f(0.796f, 3.91f);//J21
    glVertex2f(0.8f, 2.0f);//I21
    glVertex2f(1.0f, 2.0f);//L21
    glVertex2f(1.01f, 3.91f);//K21
    glEnd();

    //6
    glBegin(GL_POLYGON);
    glVertex2f(1.396f, 3.91f);//N21
    glVertex2f(1.4f, 2.0f);//I21
    glVertex2f(1.6f, 2.0f);//L21
    glVertex2f(1.61f, 3.91f);//K21
    glEnd();

    //7
    glBegin(GL_POLYGON);
    glVertex2f(1.999f, 3.91f);//N21
    glVertex2f(2.0f, 2.0f);//I21
    glVertex2f(2.23f, 2.0f);//L21
    glVertex2f(2.29f, 3.91f);//K21
    glEnd();

    //8
    glBegin(GL_POLYGON);
    glVertex2f(2.596f, 3.94f);//V21
    glVertex2f(2.6f, 2.0f);//I21
    glVertex2f(2.84f, 2.0f);//Z21
    glVertex2f(2.87f, 3.93f);//W21
    glEnd();

    //9
    glBegin(GL_POLYGON);
    glVertex2f(3.21f, 3.94f);//B22
    glVertex2f(3.2f, 2.0f);//E22
    glVertex2f(3.53f, 2.0f);//H22
    glVertex2f(3.55f, 3.94f);//C22
    glEnd();

    //10
    glBegin(GL_POLYGON);
    glVertex2f(3.93f, 3.94f);//F22
    glVertex2f(3.92f, 2.0f);//E22
    glVertex2f(4.2f, 2.0f);//H22
    glVertex2f(4.22f, 3.96f);//C22
    glEnd();

    //11
    glBegin(GL_POLYGON);
    glVertex2f(4.6f, 4.0f);//J22
    glVertex2f(4.6f, 2.0f);//I22
    glVertex2f(4.898f, 2.0f);//L22
    glVertex2f(4.956f, 4.01f);//C22
    glEnd();

    //12
    glBegin(GL_POLYGON);
    glVertex2f(5.35f, 4.02f);//N22
    glVertex2f(5.4f, 2.0f);//I22
    glVertex2f(5.67f, 2.0f);//L22
    glVertex2f(5.65f, 4.0f);//C22
    glEnd();

    //PPPPPPP
    glBegin(GL_POLYGON);
    glVertex2f(14.0f, 4.0f);//Z22
    glVertex2f(14.0f, 3.8f);//IA23
    glVertex2f(30.0f, 3.8f);//B23
    glVertex2f(30.0f, 4.0f);//N7
    glEnd();

    //1
    glBegin(GL_POLYGON);
    glVertex2f(14.0f, 3.12f);//K25
    glVertex2f(13.99f, 2.7f);//M25
    glVertex2f(30.0f, 2.82f);//N25
    glVertex2f(30.0f, 3.17f);//L25
    glEnd();

    //2
    glBegin(GL_POLYGON);
    glVertex2f(14.01f, 2.33f);//P25
    glVertex2f(14.00f, 2.0f);//025
    glVertex2f(30.0f, 2.0f);//N25
    glVertex2f(30.0f, 2.30f);//L25
    glEnd();

    //3
    glBegin(GL_POLYGON);
    glVertex2f(14.48f, 4.15f);//C23
    glVertex2f(14.5f, 2.0f);//D23
    glVertex2f(14.8f, 2.0f);//F23
    glVertex2f(14.79f, 4.16f);//L25
    glEnd();

    //4
    glBegin(GL_POLYGON);
    glVertex2f(15.49f, 4.2f);//G23
    glVertex2f(15.5f, 2.0f);//D23
    glVertex2f(15.87f, 2.0f);//F23
    glVertex2f(15.89f, 4.20f);//L25
    glEnd();

    //5
    glBegin(GL_POLYGON);
    glVertex2f(16.4f, 4.17f);//L23
    glVertex2f(16.4f, 2.02f);//K23
    glVertex2f(16.76f, 2.0f);//N23
    glVertex2f(16.75f, 4.19f);//M23
    glEnd();

    //6
    glBegin(GL_POLYGON);
    glVertex2f(17.24f, 4.21f);//P23
    glVertex2f(17.28f, 2.02f);//K23
    glVertex2f(17.67f, 2.0f);//N23
    glVertex2f(17.65f, 4.21f);//M23
    glEnd();

    //7
    glBegin(GL_POLYGON);
    glVertex2f(18.22f, 4.198f);//T23
    glVertex2f(18.29f, 2.0f);//K23
    glVertex2f(18.68f, 1.98f);//N23
    glVertex2f(18.609f, 4.18f);//M23
    glEnd();

    //8
    glBegin(GL_POLYGON);
    glVertex2f(19.42f, 4.22f);//Z23
    glVertex2f(19.44f, 2.0f);//K23
    glVertex2f(19.88f, 2.01f);//N23
    glVertex2f(19.94f, 4.21f);//M23
    glEnd();

    //9
    glBegin(GL_POLYGON);
    glVertex2f(20.65f, 4.27f);//D24
    glVertex2f(20.71f, 2.0f);//K23
    glVertex2f(21.09f, 2.01f);//N23
    glVertex2f(21.07f, 4.24f);//M23
    glEnd();

    //10
    glBegin(GL_POLYGON);
    glVertex2f(21.86f, 4.25f);//H24
    glVertex2f(21.899f, 2.0f);//K23
    glVertex2f(22.5f, 2.01f);//N23
    glVertex2f(22.43f, 4.27f);//M23
    glEnd();

    //11
    glBegin(GL_POLYGON);
    glVertex2f(23.27f, 4.29f);//L24
    glVertex2f(23.33f, 2.0f);//K23
    glVertex2f(23.83f, 2.02f);//N23
    glVertex2f(23.84f, 4.27f);//M23
    glEnd();

    //12
    glBegin(GL_POLYGON);
    glVertex2f(24.73f, 4.30f);//P24
    glVertex2f(24.75f, 2.0f);//K23
    glVertex2f(25.31f, 2.02f);//N23
    glVertex2f(25.35f, 4.27f);//M23
    glEnd();

    //13
    glBegin(GL_POLYGON);
    glVertex2f(25.98f, 4.27f);//T24
    glVertex2f(26.0f, 2.0f);//K23
    glVertex2f(26.5f, 2.0f);//N23
    glVertex2f(26.58f, 4.28f);//M23
    glEnd();

    //14
    glBegin(GL_POLYGON);
    glVertex2f(27.0f, 4.28f);//Z24
    glVertex2f(27.0f, 2.0f);//K23
    glVertex2f(27.598f, 2.03f);//N23
    glVertex2f(27.599f, 4.30f);//M23
    glEnd();

    //15
    glBegin(GL_POLYGON);
    glVertex2f(28.12f, 4.27f);//D25
    glVertex2f(28.16f, 2.0f);//K23
    glVertex2f(28.64f, 2.0f);//N23
    glVertex2f(28.65f, 4.2998f);//M23
    glEnd();

    //16
    glBegin(GL_POLYGON);
    glVertex2f(29.16f, 4.31f);//D25
    glVertex2f(29.16f, 2.0f);//K23
    glVertex2f(29.63f, 2.0f);//N23
    glVertex2f(29.64f, 4.289f);//M23
    glEnd();
}

void s2_displayField()
 {
    if (s2_isDay) {
        glClearColor(0.8f, 0.67f, 0.28f, 1.0f);
    }
    else {
        glColor3f(0.5f, 0.2f, 0.06f);
    }

    glBegin(GL_POLYGON);
    glVertex2f(0.0f, 10.26f);  // E
    glVertex2f(0.0f, 2.0f);  // D
    glVertex2f(30.0f, 2.0f);  // E
    glVertex2f(30.0f, 10.33f);  // Q7
    glEnd();

    }

void s2_display_Element() {
    glClear(GL_COLOR_BUFFER_BIT);

        if (s2_isDay) {
            glClearColor(0.53f, 0.81f, 0.98f, 1.0f);  // Light Blue Sky for Day
        }
        else
            {
        glClearColor(0.0f, 0.0f, 0.2f, 1.0f);  // Dark Blue Sky for Night
        }
        s2_displayField();
        s2_displayHouse3();
        s2_displayHouse2();
        s2_displayHouse4();
        s2_displayHouse5();
        s2_displayTree1();
        s2_drawRoad();
        s2_drawLittlePond();
        s2_displayBorder();
        s2_displayBracket();
        s2_drawBoat(21.7f + s2_boatX, 4.7f);
        s2_drawBoat(23.37f + s2_boatX, 7.77);
        s2_drawSky();
        s2_drawBushes();
        s2_displayTree2();
        s2_drawTubeWellArea();
        s2_drawGirl(s2_mannX, s2_mannY);
        s2_displayBracket();
        s2_drawSun();
        s2_drawClouds();
        S2_drawBirds();
        s2_displayTree3();
        s2_displayTree4();
        s2_displayTree5();
        s2_displayTree6();
        s2_displayTree7();
        s2_displayTree8();
        s2_drawKiteScene();
        s2_drawBoy(9.7f, 9.8f);
        s2_drawManScene();

        glFlush();
}

void s2_display_ShowElement()
    {
        glClear(GL_COLOR_BUFFER_BIT);
        s2_display_Element();
        glFlush();

    }


void s2_init()
 {
    glClearColor(0.8f, 0.67f, 0.28f, 1.0f);

    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    gluOrtho2D(0.0, 30.0, 0.0, 15.0);

}


float busPosition = -6.0f; // Position of the bus
int currentScene = 0;
bool busStopped = false;    // Current scene (0,1, 2, or 3)
bool isPaused= false;
float wheelRotation = 0.0f;
void drawCircle(float x, float y, float radius, int segments, int r, int g, int b) {
    glColor3ub(r, g, b);
    glBegin(GL_TRIANGLE_FAN);
    glVertex2f(x, y);
    for (int i = 0; i <= segments; ++i) {
        float angle = 2 * PI * i / segments;
        glVertex2f(x + radius * cos(angle), y + radius * sin(angle));
    }
    glEnd();
}
void drawRim(float x, float y, float radius, int segments, float rotationAngle) {
    // Draw the outer rim
    drawCircle(x, y, radius, segments, 0, 0, 0); // Black rim
    // Draw spokes
    glColor3ub(200, 200, 200); // Light gray for spokes
    glPushMatrix();
    glTranslatef(x, y, 0.0f);
    glRotatef(rotationAngle, 0.0f, 0.0f, 1.0f);
    for (int i = 0; i < 6; ++i) { // 6 spokes
        glBegin(GL_LINES);
        glVertex2f(0.0f, 0.0f);
        glVertex2f(radius * cos(2 * PI * i / 6), radius * sin(2 * PI * i / 6));
        glEnd();
    }
    glPopMatrix();
}
//G_bus
void Bus(){
     glPushMatrix();
    glTranslatef(busPosition, 0.0f, 0.0f);
     glLineWidth(2.3);
glBegin(GL_QUADS);
glLineWidth(2.5);
glColor3ub(211,97,53);
glVertex2f(1.1002894282422, 0.501862123567);
glVertex2f(9, 0.5);
glVertex2f(9.064047511464, 2.6974111249463);
glVertex2f(1.1015507126752, 2.6865410153385);
glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
glVertex2f(1.1002894282422, 0.501862123567);
glVertex2f(9, 0.5);
glVertex2f(9.064047511464, 2.6974111249463);
glVertex2f(1.1015507126752, 2.6865410153385);
glEnd();
  glBegin(GL_QUADS);
glColor3ub(141,153,174);
glVertex2f(8.7255145712575, 1.1672912365876);
glVertex2f(9.0, 0.85);
glVertex2f(9.064047511464, 2.34);
glVertex2f(8.7133033961815, 2.3334584563424);
glEnd();
//glLineWidth(2.5);
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
glVertex2f(8.7255145712575, 1.1672912365876);
glVertex2f(9, 0.85);
glVertex2f(9.064047511464, 2.34);
glVertex2f(8.7133033961815, 2.3334584563424);
glEnd();
//door
glBegin(GL_QUADS);
glColor3ub(141,153,174);
glVertex2f(7.8516932487674, 0.7922831847575);
glVertex2f(8.4866150607846, 0.7811385481631);
glVertex2f(8.4882383163082, 2.4139575234923);
glVertex2f(7.8592711662381, 2.421535440963);
glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
glVertex2f(7.8516932487674, 0.7922831847575);
glVertex2f(8.4866150607846, 0.7811385481631);
glVertex2f(8.4882383163082, 2.4139575234923);
glVertex2f(7.8592711662381, 2.421535440963);
glEnd();
//window
glBegin(GL_QUADS);
glColor3ub(76,86,106);
glVertex2f(2.3412742405768, 1.5164555463698);
glVertex2f(7, 1.5);
glVertex2f(7.0115649692628, 2.4313603023556);
glVertex2f(2.3439395918766, 2.4651871151062);
glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
glVertex2f(2.3412742405768, 1.5164555463698);
glVertex2f(7, 1.5);
glVertex2f(7.0115649692628, 2.4313603023556);
glVertex2f(2.3439395918766, 2.4651871151062);
glEnd();
glLineWidth(1.9);
glBegin(GL_QUADS);
glColor3ub(141,153,174);
glVertex2f(2.4328214525862, 1.6227416527284);
glVertex2f(3.2714024862375, 1.6150127952754);
glVertex2f(3.267538057511, 2.3685763969436);
glVertex2f(2.4482791674922, 2.3724408256701);
glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
glVertex2f(2.4328214525862, 1.6227416527284);
glVertex2f(3.2714024862375, 1.6150127952754);
glVertex2f(3.267538057511, 2.3685763969436);
glVertex2f(2.4482791674922, 2.3724408256701);
glEnd();
glBegin(GL_QUADS);
glColor3ub(141,153,174);
glVertex2f(3.3523637514619, 1.6092680968981);
glVertex2f(4.1640030073693, 1.602504436432);
glVertex2f(4.1695432369659, 2.3531186820376);
glVertex2f(3.3586910607675, 2.3585763969436);
glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
glVertex2f(3.3523637514619, 1.6092680968981);
glVertex2f(4.1640030073693, 1.602504436432);
glVertex2f(4.1695432369659, 2.3531186820376);
glVertex2f(3.3586910607675, 2.3585763969436);
glEnd();
glBegin(GL_QUADS);
glColor3ub(141,153,174);
glVertex2f(4.2606962402224, 1.6034195090959);
glVertex2f(5.0954128451473, 1.5995550803694);
glVertex2f(5.0876839876942, 2.3531186820376);
glVertex2f(4.2645606689489, 2.3569831107641);
glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
glVertex2f(4.2606962402224, 1.6034195090959);
glVertex2f(5.0954128451473, 1.5995550803694);
glVertex2f(5.0876839876942, 2.3531186820376);
glVertex2f(4.2645606689489, 2.3569831107641);
glEnd();
  glBegin(GL_QUADS);
glColor3ub(141,153,174);
glVertex2f(5.1819817819744, 1.5975999774767);
glVertex2f(6.0079632846344, 1.5961913931858);
glVertex2f(6.0096705666002, 2.346508706557);
glVertex2f(5.1727014196773, 2.3431186820376);
glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
glVertex2f(5.1819817819744, 1.5975999774767);
glVertex2f(6.0079632846344, 1.5961913931858);
glVertex2f(6.0096705666002, 2.346508706557);
glVertex2f(5.1727014196773, 2.3431186820376);
glEnd();
glBegin(GL_QUADS);
glColor3ub(141,153,174);
glVertex2f(6.1005840732238, 1.5946480116827);
glVertex2f(6.9246780551303, 1.5964070718637);
glVertex2f(6.9246780551303, 2.3304530653082);
glVertex2f(6.1047045431333, 2.3386940051272);
glEnd();
glBegin(GL_LINE_LOOP);
glColor3ub(0,0,0);
glVertex2f(6.1005840732238, 1.5946480116827);
glVertex2f(6.9246780551303, 1.5964070718637);
glVertex2f(6.9246780551303, 2.3304530653082);
glVertex2f(6.1047045431333, 2.3386940051272);
glEnd();
drawRim(2.5, 0.5, 0.5, 36, wheelRotation); // Front wheel
    drawRim(7.5, 0.5, 0.5, 36, wheelRotation); // Rear wheel
glPopMatrix();

}




//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////







//dimaaaaaaaa







void drawRightSemiCircle(float centerX, float centerY, float radius, int segments) {
    glBegin(GL_TRIANGLE_FAN);
    glVertex2f(centerX, centerY);  // Center of the semicircle

    // Loop to draw the semicircle (facing right)
    for (int i = 0; i <= segments; i++) {
        float angle = 3.0f * M_PI / 2.0f + M_PI * float(i) / float(segments);  // Angle range: 270° to 450° (equivalent to -90° to 90°)
        float dx = radius * cosf(angle);
        float dy = radius * sinf(angle);
        glVertex2f(centerX + dx, centerY + dy);
    }
    glEnd();
}









void first_road()
{


}

void first_left_middle_road()
{
       glLineWidth(1.0f);
    glBegin(GL_POLYGON);
    glColor3f(0.2667f, 0.3059f, 0.3529f);

    glVertex2f(0, 0);
    glVertex2f(30, 0);
    glVertex2f(29.8541748181782, 3.0292050519907);
    glVertex2f(0, 3);
    glEnd();

    // midline of the road
    glBegin(GL_POLYGON);
    glColor3f(1, 1, 1);
    glVertex2f(1, 1.8);
    glVertex2f(3, 1.8);
    glVertex2f(3, 1.6);
    glVertex2f(1, 1.6);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(1, 1.8);
    glVertex2f(3, 1.8);
    glVertex2f(3, 1.6);
    glVertex2f(1, 1.6);
    glEnd();

    // 2
    glBegin(GL_POLYGON);
    glColor3f(1, 1, 1);
    glVertex2f(5.6, 1.8);
    glVertex2f(7.4, 1.8);
    glVertex2f(7.4, 1.6);
    glVertex2f(5.6, 1.6);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(5.6, 1.8);
    glVertex2f(7.4, 1.8);
    glVertex2f(7.4, 1.6);
    glVertex2f(5.6, 1.6);
    glEnd();

    // 3
    glBegin(GL_POLYGON);
    glColor3f(1, 1, 1);
    glVertex2f(10.2, 1.8);
    glVertex2f(12, 1.8);
    glVertex2f(12, 1.6);
    glVertex2f(10.2, 1.6);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(10.2, 1.8);
    glVertex2f(12, 1.8);
    glVertex2f(12, 1.6);
    glVertex2f(10.2, 1.6);
    glEnd();

    // 4

    glBegin(GL_POLYGON);
    glColor3f(1, 1, 1);
    glVertex2f(14.6, 1.8);
    glVertex2f(16.8, 1.8);
    glVertex2f(16.8, 1.6);
    glVertex2f(14.6, 1.6);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(14.6, 1.8);
    glVertex2f(16.8, 1.8);
    glVertex2f(16.8, 1.6);
    glVertex2f(14.6, 1.6);
    glEnd();

    // 5
    glBegin(GL_POLYGON);
    glColor3f(1, 1, 1);
    glVertex2f(19.6, 1.8);
    glVertex2f(21.8, 1.8);
    glVertex2f(21.8, 1.6);
    glVertex2f(19.6, 1.6);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(19.6, 1.8);
    glVertex2f(21.8, 1.8);
    glVertex2f(21.8, 1.6);
    glVertex2f(19.6, 1.6);
    glEnd();

    // 6
    glBegin(GL_POLYGON);
    glColor3f(1, 1, 1);
    glVertex2f(24.2, 1.8);
    glVertex2f(26, 1.8);
    glVertex2f(26, 1.6);
    glVertex2f(24.2, 1.6);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(24.2, 1.8);
    glVertex2f(26, 1.8);
    glVertex2f(26, 1.6);
    glVertex2f(24.2, 1.6);
    glEnd();

    // 7
    glBegin(GL_POLYGON);
    glColor3f(1, 1, 1);
    glVertex2f(28, 1.8);
    glVertex2f(29.4, 1.8);
    glVertex2f(29.4, 1.6);
    glVertex2f(28, 1.6);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(28, 1.8);
    glVertex2f(29.4, 1.8);
    glVertex2f(29.4, 1.6);
    glVertex2f(28, 1.6);
    glEnd();

    // second middle road between the house
    // middle road 1st while line
    glBegin(GL_POLYGON);
    glColor3f(0.2667f, 0.3059f, 0.3529f);
    glVertex2f(14.0f, 0.5f);
    glVertex2f(18.0f, 0.5f);
    glVertex2f(18, 0);
    glVertex2f(14, 0);
    glEnd();

    // upper middle
    glBegin(GL_POLYGON);
    glColor3f(0.2667f, 0.3059f, 0.3529f);
    glVertex2f(14.5f, 3.0f);
    glVertex2f(17.7f, 3.0f);
    glVertex2f(17.2f, 9.0f);
    glVertex2f(15, 9);
    glEnd();

    // 4
    glBegin(GL_POLYGON);
    glColor3f(1, 1, 1);
    glVertex2f(15.9, 8);
    glVertex2f(16.1, 8);
    glVertex2f(16.1, 7);
    glVertex2f(15.9, 7);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(15.9, 8);
    glVertex2f(16.1, 8);
    glVertex2f(16.1, 7);
    glVertex2f(15.9, 7);
    glEnd();

    // 5
    glBegin(GL_POLYGON);
    glColor3f(1, 1, 1);
    glVertex2f(15.9, 5.2);
    glVertex2f(16.1, 5.2);
    glVertex2f(16.1, 4);
    glVertex2f(15.9, 4);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(15.9, 5.2);
    glVertex2f(16.1, 5.2);
    glVertex2f(16.1, 4);
    glVertex2f(15.9, 4);
    glEnd();
}
void first_box()
{
   // 1st box fll color with white
    glBegin(GL_POLYGON);
    glColor3f(1, 1, 1);
    glVertex2f(0, 15);
    glVertex2f(14, 15);
    glVertex2f(14, 3);
    glVertex2f(0, 3);

    glEnd();

    // 2nd box fill with white
    glBegin(GL_POLYGON);
    glColor3f(1, 1, 1);
    glVertex2f(17.99, 15);
    glVertex2f(30, 15);
    glVertex2f(30, 3);
    glVertex2f(18, 3);

    glEnd();
}
void dima_first_house()
{
    glLineWidth(1);
    // body
    glBegin(GL_POLYGON);
    glColor3f(0.9922f, 0.3529f, 0.4667f);
    glVertex2f(0, 10);
    glVertex2f(3.5, 10);
    glVertex2f(3.5, 5);
    glVertex2f(0, 5);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(0, 10);
    glVertex2f(3.5, 10);
    glVertex2f(3.5, 5);
    glVertex2f(0, 5);
    glEnd();

    // door up
    // 1st
    glBegin(GL_POLYGON);
    glColor3f(0.0980f, 0.2706f, 0.4039f);
    glVertex2f(0.25, 6.2);
    glVertex2f(0.9997, 6.2);
    glVertex2f(1, 5.95);
    glVertex2f(0.25, 5.95);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(0.25, 6.2);
    glVertex2f(0.9997, 6.2);
    glVertex2f(1, 5.95);
    glVertex2f(0.25, 5.95);
    glEnd();

    // 2nd
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.4706f, 0.4000f);
    glVertex2f(0.9997, 6.2);
    glVertex2f(1.6, 6.2);
    glVertex2f(1.6, 5.95);
    glVertex2f(1, 5.95);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(0.9997, 6.2);
    glVertex2f(1.6, 6.2);
    glVertex2f(1.6, 5.95);
    glVertex2f(1, 5.95);
    glEnd();

    // 3rd
    glBegin(GL_POLYGON);
    glColor3f(0.9961f, 0.4078f, 0.4706f);
    glVertex2f(1.6, 6.2);
    glVertex2f(2.5, 6.2);
    glVertex2f(2.5, 5.95);
    glVertex2f(1.6, 5.95);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(1.6, 6.2);
    glVertex2f(2.5, 6.2);
    glVertex2f(2.5, 5.95);
    glVertex2f(1.6, 5.95);
    glEnd();
    // 4th
    glBegin(GL_POLYGON);
    glColor3f(0.0980f, 0.2706f, 0.4039f);
    glVertex2f(2.5, 6.2);
    glVertex2f(3.1, 6.2);
    glVertex2f(3.1, 5.95);
    glVertex2f(2.5, 5.95);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(2.5, 6.2);
    glVertex2f(3.1, 6.2);
    glVertex2f(3.1, 5.95);
    glVertex2f(2.5, 5.95);
    glEnd();

    // door down 1st
    glBegin(GL_POLYGON);
    glColor3f(0.1765f, 0.5373f, 0.7294f);

    glVertex2f(0.40003, 5.95);
    glVertex2f(1, 5.95);
    glVertex2f(1, 5);
    glVertex2f(0.40013, 5.0028);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(0.40003, 5.95);
    glVertex2f(1, 5.95);
    glVertex2f(1, 5);
    glVertex2f(0.40013, 5.0028);
    glEnd();

    // door down 2nd
    glBegin(GL_POLYGON);
    glColor3f(0.8275f, 0.9961f, 0.9725f);
    glVertex2f(1, 5.95);
    glVertex2f(1.5, 5.95);
    glVertex2f(1.5, 5);
    glVertex2f(1, 5);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(1, 5.95);
    glVertex2f(1.5, 5.95);
    glVertex2f(1.5, 5);
    glVertex2f(1, 5);
    glEnd();

    // door handle

    glBegin(GL_POLYGON);
    glColor3f(1, 1, 1);
    glVertex2f(1.35, 5.6);
    glVertex2f(1.45, 5.6);
    glVertex2f(1.45, 5.25);
    glVertex2f(1.35, 5.25);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(1.35, 5.6);
    glVertex2f(1.45, 5.6);
    glVertex2f(1.45, 5.25);
    glVertex2f(1.35, 5.25);
    glEnd();

    // second door handle

    glBegin(GL_POLYGON);
    glColor3f(0, 1, 1);
    glVertex2f(1.55, 5.6);
    glVertex2f(1.65, 5.6);
    glVertex2f(1.65, 5.25);
    glVertex2f(1.55, 5.25);
    glEnd();

    // door down third

    glBegin(GL_POLYGON);
    glColor3f(0.6824f, 0.8706f, 0.8706f);
    glVertex2f(1.5, 5.95);
    glVertex2f(2.2, 5.95);
    glVertex2f(2.2, 5);
    glVertex2f(1.5, 5);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(1.5, 5.95);
    glVertex2f(2.2, 5.95);
    glVertex2f(2.2, 5);
    glVertex2f(1.5, 5);
    glEnd();

    // door down 4th

    glBegin(GL_POLYGON);
    glColor3f(0.6118f, 0.9451f, 0.9608f);
    glVertex2f(2.2, 5.95);
    glVertex2f(3, 5.949);
    glVertex2f(3, 5.0005);
    glVertex2f(2.2, 5);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(2.2, 5.95);
    glVertex2f(3, 5.949);
    glVertex2f(3, 5.0005);
    glVertex2f(2.2, 5);
    glEnd();

    // door upper shadow

    glBegin(GL_POLYGON);
    glColor3f(0.0980f, 0.2706f, 0.4039f);
    glVertex2f(0.25, 5.95);
    glVertex2f(3.1, 5.95);
    glVertex2f(3.2, 5.8);
    glVertex2f(0.45, 5.8);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(0.25, 5.95);
    glVertex2f(3.1, 5.95);
    glVertex2f(3.2, 5.8);
    glVertex2f(0.45, 5.8);
    glEnd();

    // 1st window 1st part

    glBegin(GL_POLYGON);
    glColor3f(0.1451, 0.4745, 0.7529);

    glVertex2f(0.8, 9.4);
    glVertex2f(1.4, 9.4);
    glVertex2f(1.4, 8.6);

    glEnd();

    // 1st wnodow second part
    glBegin(GL_POLYGON);
    glColor3f(0.5294, 0.9608, 0.9922);
    glVertex2f(0.8, 9.4);
    glVertex2f(1.4, 8.6);
    glVertex2f(0.8, 8.6);

    glEnd();

    // 1st window border

    glBegin(GL_POLYGON);
    glColor3f(1, 1, 1);
    glVertex2f(0.75, 9.45);
    glVertex2f(1.4, 9.4);
    glVertex2f(1.4, 9.4);
    glVertex2f(0.8, 9.4);
    glEnd();

    glBegin(GL_POLYGON);
    glColor3f(1, 1, 1);
    glVertex2f(1.4, 9.4);
    glVertex2f(1.42, 8.58);
    glVertex2f(1.4, 8.6);
    glVertex2f(1.4, 9.4);
    glEnd();

    // 2nd window 1st part
    glBegin(GL_POLYGON);
    glColor3f(0.1451, 0.4745, 0.7529);
    glVertex2f(2.4, 9.4);
    glVertex2f(3, 9.4);
    glVertex2f(3, 8.6);
    glEnd();

    // 2nd window 2nd part
    glBegin(GL_POLYGON);
    glColor3f(0.5294, 0.9608, 0.9922);
    glVertex2f(2.4, 9.4);
    glVertex2f(3, 8.6);
    glVertex2f(2.4, 8.6);
    glEnd();

    // 3rd window 1st part
    glBegin(GL_POLYGON);
    glColor3f(0.1451, 0.4745, 0.7529);
    glVertex2f(0.8, 7.8);
    glVertex2f(1.4, 7.8);
    glVertex2f(1.4, 7);
    glEnd();

    // 3rd window 2nd part
    glBegin(GL_POLYGON);
    glColor3f(0.5294, 0.9608, 0.9922);
    glVertex2f(0.8, 7.8);
    glVertex2f(0.8, 7);
    glVertex2f(1.4, 7);
    glEnd();

    // 4th window 1st part
    glBegin(GL_POLYGON);
    glColor3f(0.1451, 0.4745, 0.7529);
    glVertex2f(2.4, 7.8);
    glVertex2f(3, 7.8);
    glVertex2f(3, 7);
    glEnd();

    // 4th window 2nd part
    glBegin(GL_POLYGON);
    glColor3f(0.5294, 0.9608, 0.9922);
    glVertex2f(2.4, 7.8);
    glVertex2f(3, 7);
    glVertex2f(2.4, 7);
    glEnd();
}

// building2

void dima_second_house()
{
    glLineWidth(1);

    // roof

    glBegin(GL_POLYGON);
    glColor3f(0.2431f, 0.3686f, 0.5608f);
    glVertex2f(3.5f, 11.8f);
    glVertex2f(6.3f, 11.8f);
    glVertex2f(6.3f, 11.5f);
    glVertex2f(3.5f, 11.5f);
    glEnd();

    // Black border
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glBegin(GL_LINE_LOOP);
    glVertex2f(3.5f, 11.8f);
    glVertex2f(6.3f, 11.8f);
    glVertex2f(6.3f, 11.5f);
    glVertex2f(3.5f, 11.5f);
    glEnd();
    // Body of the house

    glBegin(GL_POLYGON);
    glColor3f(0.4588f, 0.8667f, 0.9804f);
    glVertex2f(3.5f, 11.5f);
    glVertex2f(6.3f, 11.5f);
    glVertex2f(6.3f, 5.0f);
    glVertex2f(3.5f, 5.0f);
    glEnd();

    // Black border
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glBegin(GL_LINE_LOOP);
    glVertex2f(3.5f, 11.5f);
    glVertex2f(6.3f, 11.5f);
    glVertex2f(6.3f, 5.0f);
    glVertex2f(3.5f, 5.0f);
    glEnd();




    // window 3 part 1
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(4.0f, 9.7f);
    glVertex2f(4.5f, 9.7f);
    glVertex2f(4.5, 9.2);

    glEnd();

    // part 2
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.94f, 0.84f);
    glVertex2f(4.0f, 9.7f);
    glVertex2f(4.5f, 9.2f);
    glVertex2f(4.0f, 9.2f);
    glEnd();

    // window 4  part 1
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(5.3f, 9.7f);
    glVertex2f(5.8f, 9.2f);
    glVertex2f(5.8f, 9.7f);
    glEnd();
    // part 2
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.94f, 0.84f);
    glVertex2f(5.3f, 9.7f);
    glVertex2f(5.8f, 9.2f);
    glVertex2f(5.3f, 9.2f);
    glEnd();

    // window 5  part 1
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(4.0f, 8.6f);
    glVertex2f(4.5f, 8.1f);
    glVertex2f(4.5f, 8.6f);
    glEnd();
    // part 2
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.94f, 0.84f);
    glVertex2f(4.0f, 8.6f);
    glVertex2f(4.5f, 8.1f);
    glVertex2f(4.0f, 8.1f);
    glEnd();

    // window 6  part 1
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(5.3f, 8.6f);
    glVertex2f(5.8f, 8.1f);
    glVertex2f(5.8f, 8.6f);
    glEnd();

    // part 2
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.94f, 0.84f);
    glVertex2f(5.3f, 8.6f);
    glVertex2f(5.8f, 8.1f);
    glVertex2f(5.3f, 8.1f);
    glEnd();

    // window 7  part 1
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(4.0f, 7.5f);
    glVertex2f(4.5f, 7.0f);
    glVertex2f(4.5f, 7.5f);
    glEnd();

    // part 2
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.94f, 0.84f);
    glVertex2f(4.0f, 7.5f);
    glVertex2f(4.5f, 7.0f);
    glVertex2f(4.0f, 7.0f);
    glEnd();

    // window 8  part 1
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(5.3f, 7.5f);
    glVertex2f(5.8f, 7.0f);
    glVertex2f(5.8f, 7.5f);
    glEnd();

    // part 2
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.94f, 0.84f);
    glVertex2f(5.3f, 7.5f);
    glVertex2f(5.8f, 7.0f);
    glVertex2f(5.3f, 7.0f);
    glEnd();

    // gate
    // from leftmost rectangle

    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(4.1f, 5.0f);
    glVertex2f(4.1f, 6.0f);
    glVertex2f(4.5f, 6.0f);
    glVertex2f(4.5f, 5.0f);
    glEnd();

    // after that
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(4.9f, 5.0f);
    glVertex2f(4.9f, 6.0f);
    glVertex2f(5.5f, 6.0f);
    glVertex2f(5.5f, 5.0f);
    glEnd();

    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(5.5f, 5.0f);
    glVertex2f(5.5f, 6.0f);
    glVertex2f(4.5f, 6.0f);
    glVertex2f(4.5f, 5.0f);
    glEnd();

    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(5.5f, 5.0f);
    glVertex2f(5.5f, 6.0f);
    glVertex2f(5.8f, 6.0f);
    glVertex2f(5.8f, 5.0f);
    glEnd();

    // left handle
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.94f, 0.84f);
    glVertex2f(4.82f, 5.6f);
    glVertex2f(4.86f, 5.6f);
    glVertex2f(4.86f, 5.46f);
    glVertex2f(4.82f, 5.46f);
    glEnd();

    // right handle

    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.94f, 0.84f);
    glVertex2f(4.94f, 5.6f);
    glVertex2f(4.98f, 5.6f);
    glVertex2f(4.98f, 5.46f);
    glVertex2f(4.94f, 5.46f);
    glEnd();

    // middle
    glBegin(GL_POLYGON);
    glColor3f(0.098, 0.271, 0.404);
    glVertex2f(4.0f, 6.0f);
    glVertex2f(4.2f, 5.8f);
    glVertex2f(6.0f, 5.8f);
    glVertex2f(5.88f, 6.0f);
    glEnd();

    // top mosr left
    glBegin(GL_POLYGON);
    glColor3f(0.098, 0.271, 0.404);
    glVertex2f(4.0f, 6.25f);
    glVertex2f(4.1f, 6.25f);
    glVertex2f(4.1f, 6.0f);
    glVertex2f(4.0f, 6.0f);
    glEnd();

    // after
    glBegin(GL_POLYGON);
    glColor3f(0.098, 0.271, 0.404);
    glVertex2f(4.1f, 6.25f);
    glVertex2f(4.1f, 6.0f);
    glVertex2f(4.5f, 6.0f);
    glVertex2f(4.5f, 6.25f);
    glEnd();
    // after
    glBegin(GL_POLYGON);
    glColor3f(0.098, 0.271, 0.404);
    glVertex2f(4.1f, 6.25f);
    glVertex2f(4.1f, 6.0f);
    glVertex2f(5.0f, 6.0f);
    glVertex2f(5.0f, 6.25f);
    glEnd();

    // after
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.4706f, 0.4000f);
    glVertex2f(5.0f, 6.0f);
    glVertex2f(5.0f, 6.25f);
    glVertex2f(5.4f, 6.25f);
    glVertex2f(5.4f, 6.0f);
    glEnd();

    // after
    glBegin(GL_POLYGON);
    glColor3f(0.098, 0.271, 0.404);
    glVertex2f(5.4f, 6.25f);
    glVertex2f(5.4f, 6.0f);
    glVertex2f(5.8f, 6.0f);
    glVertex2f(5.8f, 6.25f);
    glEnd();

    // after
    glBegin(GL_POLYGON);
    glColor3f(0.098, 0.271, 0.404);
    glVertex2f(5.8f, 6.25f);
    glVertex2f(5.8f, 6.0f);
    glVertex2f(5.88f, 6.0f);
    glVertex2f(5.88f, 6.25f);
    glEnd();
}




void third_house()
{
    glLineWidth(1);
    // roof
    glBegin(GL_POLYGON);
    glColor3f(0.098, 0.271, 0.404);
    glVertex2f(6.3f, 11.0f);
    glVertex2f(6.3f, 10.5f);
    glVertex2f(9.5f, 10.5f);
    glVertex2f(10.0f, 11.0f);
    glEnd();

    // border
    glBegin(GL_POLYGON);
    glColor3f(0.988, 0.471, 0.384);
    glVertex2f(6.3f, 11.0f);
    glVertex2f(6.3f, 10.5f);
    glVertex2f(9.5f, 10.5f);
    glVertex2f(10.0f, 11.0f);
    glEnd();

    // base
    glBegin(GL_POLYGON);
    glColor3f(0.988, 0.471, 0.384);
    glVertex2f(6.3f, 10.5f);
    glVertex2f(9.5f, 10.5f);
    glVertex2f(9.5f, 5.0f);
    glVertex2f(6.3f, 5.0f);
    glEnd();

    // border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(6.3f, 10.5f);
    glVertex2f(9.5f, 10.5f);
    glVertex2f(9.5f, 5.0f);
    glVertex2f(6.3f, 5.0f);
    glEnd();

    // right base
    glBegin(GL_POLYGON);
    glColor3f(0.988, 0.471, 0.384);
    glVertex2f(9.5f, 10.5f);
    glVertex2f(10.0f, 11.0f);
    glVertex2f(10.0f, 5.5f);
    glVertex2f(9.5f, 5.0f);
    glEnd();

    // border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(9.5f, 10.5f);
    glVertex2f(10.0f, 11.0f);
    glVertex2f(10.0f, 5.5f);
    glVertex2f(9.5f, 5.0f);
    glEnd();

    // door left most squere
    glBegin(GL_POLYGON);
    glColor3f(0.176, 0.537, 0.729);
    glVertex2f(7.2f, 5.8f);
    glVertex2f(7.5f, 5.8f);
    glVertex2f(7.5f, 5.0f);
    glVertex2f(7.2f, 5.0f);
    glEnd();

    // border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(7.2f, 5.8f);
    glVertex2f(7.5f, 5.8f);
    glVertex2f(7.5f, 5.0f);
    glVertex2f(7.2f, 5.0f);
    glEnd();

    // left middle

    glBegin(GL_POLYGON);
    glColor3f(0.176, 0.537, 0.729);
    glVertex2f(7.5f, 6.0f);
    glVertex2f(7.5f, 5.0f);
    glVertex2f(8.0f, 5.0f);
    glVertex2f(8.0f, 6.0f);
    glEnd();

    // border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(7.5f, 6.0f);
    glVertex2f(7.5f, 5.0f);
    glVertex2f(8.0f, 5.0f);
    glVertex2f(8.0f, 6.0f);
    glEnd();

    // rightmiddle
    glBegin(GL_POLYGON);
    glColor3f(0.176, 0.537, 0.729);
    glVertex2f(8.0f, 5.0f);
    glVertex2f(8.0f, 6.0f);
    glVertex2f(8.5f, 6.0f);
    glVertex2f(8.5f, 5.0f);
    glEnd();

    // border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(8.0f, 5.0f);
    glVertex2f(8.0f, 6.0f);
    glVertex2f(8.5f, 6.0f);
    glVertex2f(8.5f, 5.0f);
    glEnd();

    // right
    glBegin(GL_POLYGON);
    glColor3f(0.176, 0.537, 0.729);
    glVertex2f(8.5f, 5.8f);
    glVertex2f(8.8f, 5.8f);
    glVertex2f(8.8f, 5.0f);
    glVertex2f(8.5f, 5.0f);
    glEnd();

    // border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(8.5f, 5.8f);
    glVertex2f(8.8f, 5.8f);
    glVertex2f(8.8f, 5.0f);
    glVertex2f(8.5f, 5.0f);
    glEnd();

    // handle left
    glBegin(GL_POLYGON);
    glColor3f(1.0, 1.0, 1.0);
    glVertex2f(7.85f, 5.7f);
    glVertex2f(7.95f, 5.7f);
    glVertex2f(7.95f, 5.45f);
    glVertex2f(7.85f, 5.45f);
    glEnd();
    // border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(7.85f, 5.7f);
    glVertex2f(7.95f, 5.7f);
    glVertex2f(7.95f, 5.45f);
    glVertex2f(7.85f, 5.45f);
    glEnd();

    // handle right
    glBegin(GL_POLYGON);
    glColor3f(1.0, 1.0, 1.0);
    glVertex2f(8.05f, 5.7f);
    glVertex2f(8.15f, 5.7f);
    glVertex2f(8.15f, 5.45f);
    glVertex2f(8.05f, 5.45f);
    glEnd();

    // border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(8.05f, 5.7f);
    glVertex2f(8.15f, 5.7f);
    glVertex2f(8.15f, 5.45f);
    glVertex2f(8.05f, 5.45f);
    glEnd();

    // window 1 part 1
    glBegin(GL_POLYGON);
    glColor3f(0.145, 0.475, 0.753);
    glVertex2f(7.0f, 10.0f);
    glVertex2f(7.6f, 10.0f);
    glVertex2f(7.6f, 9.4f);
    glEnd();

    // window 1 part 2
    glBegin(GL_POLYGON);
    glColor3f(0.529, 0.961, 0.992);
    glVertex2f(7.0f, 9.4f);
    glVertex2f(7.0f, 10.0f);
    glVertex2f(7.6f, 9.4f);
    glEnd();

    // window 2 part 1
    glBegin(GL_POLYGON);
    glColor3f(0.145, 0.475, 0.753);
    glVertex2f(8.4f, 10.0f);
    glVertex2f(9.0f, 10.0f);
    glVertex2f(9.0f, 9.4f);
    glEnd();

    // window 2 part 2
    glBegin(GL_POLYGON);
    glColor3f(0.529, 0.961, 0.992);
    glVertex2f(8.4f, 10.0f);
    glVertex2f(9.0f, 9.4f);
    glVertex2f(8.4f, 9.4f);
    glEnd();

    // window 3 part 1
    glBegin(GL_POLYGON);
    glColor3f(0.145, 0.475, 0.753);
    glVertex2f(7.0f, 8.6f);
    glVertex2f(7.6f, 8.0f);
    glVertex2f(7.6f, 8.6f);
    glEnd();

    // window 3 part 2
    glBegin(GL_POLYGON);
    glColor3f(0.529, 0.961, 0.992);
    glVertex2f(7.0f, 8.6f);
    glVertex2f(7.6f, 8.0f);
    glVertex2f(7.0f, 8.0f);
    glEnd();

    // window 4 part 1
    glBegin(GL_POLYGON);
    glColor3f(0.145, 0.475, 0.753);
    glVertex2f(8.4f, 8.6f);
    glVertex2f(9.0f, 8.0f);
    glVertex2f(9.0f, 8.6f);
    glEnd();

    // window 4 part 2
    glBegin(GL_POLYGON);
    glColor3f(0.529, 0.961, 0.992);
    glVertex2f(8.4f, 8.6f);
    glVertex2f(9.0f, 8.0f);
    glVertex2f(8.4f, 8.0f);
    glEnd();

    // window 5 part 1
    glBegin(GL_POLYGON);
    glColor3f(0.145, 0.475, 0.753);
    glVertex2f(7.0f, 7.2f);
    glVertex2f(7.6f, 6.6f);
    glVertex2f(7.6f, 7.2f);
    glEnd();

    // window 5 part 2
    glBegin(GL_POLYGON);
    glColor3f(0.529, 0.961, 0.992);
    glVertex2f(7.0f, 7.2f);
    glVertex2f(7.6f, 6.6f);
    glVertex2f(7.0f, 6.6f);
    glEnd();

    // window 6 part 1
    glBegin(GL_POLYGON);
    glColor3f(0.145, 0.475, 0.753);
    glVertex2f(8.4f, 7.2f);
    glVertex2f(9.0f, 6.6f);
    glVertex2f(9.0f, 7.2f);
    glEnd();

    // window 6 part 2
    glBegin(GL_POLYGON);
    glColor3f(0.529, 0.961, 0.992);
    glVertex2f(8.4f, 7.2f);
    glVertex2f(9.0f, 6.6f);
    glVertex2f(8.4f, 6.6f);
    glEnd();
}

void forth_house()
{
    glLineWidth(1);
    // roof
    glBegin(GL_POLYGON);
  glColor3f(0.980, 0.753, 0.271);

    glVertex2f(25.0f, 10.0f);
    glVertex2f(26.0f, 11.0f);
    glVertex2f(30.0f, 11.0f);
    glVertex2f(29.0f, 10.0f);
    glEnd();
    // border
     glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(25.0f, 10.0f);
    glVertex2f(26.0f, 11.0f);
    glVertex2f(30.0f, 11.0f);
    glVertex2f(29.0f, 10.0f);
    glEnd();

    // base
    glBegin(GL_POLYGON);
  glColor3f(0.980, 0.753, 0.271);

    glVertex2f(25.0f, 10.0f);
    glVertex2f(29.0f, 10.0f);
    glVertex2f(29.0f, 5.0f);
    glVertex2f(25.0f, 5.0f);
    glEnd();

   // border
     glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(25.0f, 10.0f);
    glVertex2f(29.0f, 10.0f);
    glVertex2f(29.0f, 5.0f);
    glVertex2f(25.0f, 5.0f);
    glEnd();

    // base right
    glBegin(GL_POLYGON);
  glColor3f(0.980, 0.753, 0.271);

    glVertex2f(29.0f, 10.0f);
    glVertex2f(30.0f, 11.0f);
    glVertex2f(30.0f, 5.0f);
    glVertex2f(29.0f, 5.0f);
    glEnd();


       // border
     glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
   glVertex2f(29.0f, 10.0f);
    glVertex2f(30.0f, 11.0f);
    glVertex2f(30.0f, 5.0f);
    glVertex2f(29.0f, 5.0f);
    glEnd();




  // window 1 part 1
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(25.8, 9.4);
    glVertex2f(26.6, 9.4);
    glVertex2f(26.6, 8.4);

    glEnd();


      glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
     glVertex2f(25.8, 9.4);
    glVertex2f(26.6, 9.4);
    glVertex2f(26.6, 8.4);

    glEnd();

    // part 2
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.94f, 0.84f);
  glVertex2f(25.8, 9.4);
    glVertex2f(26.6, 8.4);
    glVertex2f(25.8, 8.4);
    glEnd();



//border
        glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
   glVertex2f(25.8, 9.4);
    glVertex2f(26.6, 8.4);
  glVertex2f(25.8, 8.4);
    glEnd();



    //window 2 part1

       glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(27.4, 9.4);
    glVertex2f(28.2, 9.4);
    glVertex2f(28.2, 8.4);

    glEnd();


      glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color

    glVertex2f(27.4, 9.4);
    glVertex2f(28.2, 9.4);
    glVertex2f(28.2, 8.4);

    glEnd();






    // part 2
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.94f, 0.84f);
   glVertex2f(27.4, 9.4);
    glVertex2f(28.2, 8.4);
  glVertex2f(27.4, 8.4);
    glEnd();



//border
        glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
   glVertex2f(27.4, 9.4);
    glVertex2f(28.2, 8.4);
  glVertex2f(27.4, 8.4);
    glEnd();




    //window 3 part1

       glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(25.8, 7.6);
    glVertex2f(26.6, 7.6);
    glVertex2f(26.6, 6.8);

    glEnd();


      glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(25.8, 7.6);
    glVertex2f(26.6, 7.6);
    glVertex2f(26.6, 6.8);

    glEnd();






    // part 2
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.94f, 0.84f);
   glVertex2f(25.8, 7.6);
    glVertex2f(26.6, 6.8);
  glVertex2f(25.8, 6.8);
    glEnd();



//border
        glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
   glVertex2f(25.8, 7.6);
    glVertex2f(26.6, 6.8);
  glVertex2f(25.8, 6.8);
    glEnd();


    //window 4 part1

       glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(27.4, 7.6);
    glVertex2f(28.2, 7.6);
    glVertex2f(28.2, 6.8);

    glEnd();


      glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(27.4, 7.6);
    glVertex2f(28.2, 7.6);
    glVertex2f(28.2, 6.8);

    glEnd();



    // part 2
    glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.94f, 0.84f);
   glVertex2f(27.4, 7.6);
    glVertex2f(28.2, 6.8);
  glVertex2f(27.4, 6.8);
    glEnd();



//border
        glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
   glVertex2f(27.4, 7.6);
    glVertex2f(28.2, 6.8);
  glVertex2f(27.4, 6.8);
    glEnd();




    //door
         glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(26, 6);
    glVertex2f(26.5, 6);
    glVertex2f(26.5, 5);
     glVertex2f(26, 5);


    glEnd();


         glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(26, 6);
    glVertex2f(26.5, 6);
    glVertex2f(26.5, 5);
     glVertex2f(26, 5);
    glEnd();




        //door
         glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(26, 6);
    glVertex2f(26.5, 6);
    glVertex2f(26.5, 5);
     glVertex2f(26, 5);


    glEnd();


         glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(26, 6);
    glVertex2f(26.5, 6);
    glVertex2f(26.5, 5);
     glVertex2f(26, 5);
    glEnd();


       //door 2
         glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(26.5, 6.2);
    glVertex2f(27, 6.2);
    glVertex2f(27, 5);
     glVertex2f(26.5, 5);


    glEnd();


         glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
   glVertex2f(26.5, 6.2);
    glVertex2f(27, 6.2);
    glVertex2f(27, 5);
     glVertex2f(26.5, 5);

    glEnd();




         //door 3
         glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(27, 6.2);
    glVertex2f(27.5, 6.2);
    glVertex2f(27.5, 5);
       glVertex2f(27, 5);


    glEnd();


         glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
  glVertex2f(27, 6.2);
    glVertex2f(27.5, 6.2);
    glVertex2f(27.5, 5);
       glVertex2f(27, 5);

    glEnd();



         //door 4
         glBegin(GL_POLYGON);
    glColor3f(1.0f, 0.75f, 0.8f);
    glVertex2f(27.5, 6);
    glVertex2f(28, 6);
    glVertex2f(28, 5);
       glVertex2f(27.5, 5);


    glEnd();


         glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(27.5, 6);
    glVertex2f(28, 6);
    glVertex2f(28, 5);
       glVertex2f(27.5, 5);


    glEnd();

//handle

      glBegin(GL_POLYGON);
    glColor3f(0,0,0);
    glVertex2f(26.8, 5.6);
    glVertex2f(26.9, 5.6);
    glVertex2f(26.9, 5.35);
       glVertex2f(26.8, 5.35);


    glEnd();



      glBegin(GL_POLYGON);
    glColor3f(0,0,0);
    glVertex2f(27.1, 5.6);
    glVertex2f(27.2, 5.6);
    glVertex2f(27.2, 5.35);
       glVertex2f(27.1, 5.35);


    glEnd();



}

// fifth house
void fifth_house()
{

    glLineWidth(1);
    // roof
    glBegin(GL_POLYGON);
    glColor3f(0.702f, 0.518f, 0.937f);
    glVertex2f(22.0f, 11.7f);
    glVertex2f(25.5f, 11.7f);
    glVertex2f(25.0f, 11.0f);
    glVertex2f(21.4f, 11.0f);

    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(22.0f, 11.7f);
    glVertex2f(25.5f, 11.7f);
    glVertex2f(25.0f, 11.0f);
    glVertex2f(21.4f, 11.0f);
    glEnd();
    // base
    glBegin(GL_POLYGON);
    glColor3f(0.702f, 0.518f, 0.937f);
    glVertex2f(21.4f, 11.0f);
    glVertex2f(25.0f, 11.0f);
    glVertex2f(25.0f, 5.0f);
    glVertex2f(21.4f, 5.0f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(21.4f, 11.0f);
    glVertex2f(25.0f, 11.0f);
    glVertex2f(25.0f, 5.0f);
    glVertex2f(21.4f, 5.0f);
    glEnd();

    // right side
    glBegin(GL_POLYGON);
    glColor3f(0.702f, 0.518f, 0.937f);
    glVertex2f(25.0f, 11.0f);
    glVertex2f(25.5f, 11.7f);
    glVertex2f(25.5f, 10.5f);
    glVertex2f(25.0f, 10.0f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(25.0f, 11.0f);
    glVertex2f(25.5f, 11.7f);
    glVertex2f(25.5f, 10.5f);
    glVertex2f(25.0f, 10.0f);
    glEnd();

    // gate
    // left
    glBegin(GL_POLYGON);
    glColor3f(0.176f, 0.537f, 0.741f);
    glVertex2f(22.2f, 5.8f);
    glVertex2f(22.6f, 5.8f);
    glVertex2f(22.6f, 5.0f);
    glVertex2f(22.2f, 5.0f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(22.2f, 5.8f);
    glVertex2f(22.6f, 5.8f);
    glVertex2f(22.6f, 5.0f);
    glVertex2f(22.2f, 5.0f);
    glEnd();
    // middle left
    glBegin(GL_POLYGON);
    glColor3f(0.827f, 0.957f, 0.973f);
    glVertex2f(23.2f, 6.2f);
    glVertex2f(23.2f, 5.0f);
    glVertex2f(22.6f, 5.0f);
    glVertex2f(22.6f, 6.2f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(23.2f, 6.2f);
    glVertex2f(23.2f, 5.0f);
    glVertex2f(22.6f, 5.0f);
    glVertex2f(22.6f, 6.2f);
    glEnd();

    // middle right
    glBegin(GL_POLYGON);
    glColor3f(0.612f, 0.945f, 0.961f);
    glVertex2f(23.2f, 6.2f);
    glVertex2f(23.2f, 5.0f);
    glVertex2f(23.8f, 5.0f);
    glVertex2f(23.8f, 6.2f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(23.2f, 6.2f);
    glVertex2f(23.2f, 5.0f);
    glVertex2f(23.8f, 5.0f);
    glVertex2f(23.8f, 6.2f);
    glEnd();

    // right
    glBegin(GL_POLYGON);
    glColor3f(0.176f, 0.537f, 0.729f);
    glVertex2f(23.8f, 5.8f);
    glVertex2f(24.2f, 5.8f);
    glVertex2f(24.2f, 5.0f);
    glVertex2f(23.8f, 5.0f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(23.8f, 5.8f);
    glVertex2f(24.2f, 5.8f);
    glVertex2f(24.2f, 5.0f);
    glVertex2f(23.8f, 5.0f);
    glEnd();

    // left handle
    glBegin(GL_POLYGON);
    glColor3f(0.176f, 0.537f, 0.729f);
    glVertex2f(23.0f, 5.7f);
    glVertex2f(23.1f, 5.7f);
    glVertex2f(23.1f, 5.4f);
    glVertex2f(23.0f, 5.4f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(23.0f, 5.7f);
    glVertex2f(23.1f, 5.7f);
    glVertex2f(23.1f, 5.4f);
    glVertex2f(23.0f, 5.4f);
    glEnd();

    // right handle
    glBegin(GL_POLYGON);
    glColor3f(0.176f, 0.537f, 0.729f);
    glVertex2f(23.3f, 5.7f);
    glVertex2f(23.4f, 5.7f);
    glVertex2f(23.4f, 5.4f);
    glVertex2f(23.3f, 5.4f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(23.3f, 5.7f);
    glVertex2f(23.4f, 5.7f);
    glVertex2f(23.4f, 5.4f);
    glVertex2f(23.3f, 5.4f);
    glEnd();

    // window 1 part 1
    glBegin(GL_POLYGON);
    glColor3f(0.176f, 0.537f, 0.729f);
    glVertex2f(22.0f, 10.4f);
    glVertex2f(22.8f, 9.6f);
    glVertex2f(22.8f, 10.4f);
    glEnd();

    // window 1 part 2
    glBegin(GL_POLYGON);
    glColor3f(0.529, 0.961, 0.992);
    glVertex2f(22.0f, 10.4f);
    glVertex2f(22.8f, 9.6f);
    glVertex2f(22.0f, 9.6f);
    glEnd();

    // window 2 part 1
    glBegin(GL_POLYGON);
    glColor3f(0.176f, 0.537f, 0.729f);
    glVertex2f(23.6f, 10.4f);
    glVertex2f(24.4f, 9.6f);
    glVertex2f(24.4f, 10.4f);
    glEnd();

    // window 2 part 2
    glBegin(GL_POLYGON);
    glColor3f(0.529, 0.961, 0.992);
    glVertex2f(23.6f, 10.4f);
    glVertex2f(24.4f, 9.6f);
    glVertex2f(23.6f, 9.6f);
    glEnd();

    // window 3 part 1
    glBegin(GL_POLYGON);
    glColor3f(0.176f, 0.537f, 0.729f);
    glVertex2f(22.0f, 9.0f);
    glVertex2f(22.8f, 8.2f);
    glVertex2f(22.8f, 9.0f);
    glEnd();

    // window 3 part 2
    glBegin(GL_POLYGON);
    glColor3f(0.529, 0.961, 0.992);
    glVertex2f(22.0f, 9.0f);
    glVertex2f(22.8f, 8.2f);
    glVertex2f(22.0f, 8.2f);
    glEnd();

    // window 4 part 1
    glBegin(GL_POLYGON);
    glColor3f(0.176f, 0.537f, 0.729f);
    glVertex2f(23.6f, 9.0f);
    glVertex2f(24.4f, 8.2f);
    glVertex2f(24.4f, 9.0f);
    glEnd();

    // window 4 part 2
    glBegin(GL_POLYGON);
    glColor3f(0.529, 0.961, 0.992);
    glVertex2f(23.6f, 9.0f);
    glVertex2f(24.4f, 8.2f);
    glVertex2f(23.6f, 8.2f);
    glEnd();

    // window 5 part 1
    glBegin(GL_POLYGON);
    glColor3f(0.176f, 0.537f, 0.729f);
    glVertex2f(22.0f, 7.6f);
    glVertex2f(22.8f, 6.8f);
    glVertex2f(22.8f, 7.6f);
    glEnd();

    // window 5 part 2
    glBegin(GL_POLYGON);
    glColor3f(0.529, 0.961, 0.992);
    glVertex2f(22.0f, 7.6f);
    glVertex2f(22.8f, 6.8f);
    glVertex2f(22.0f, 6.8f);
    glEnd();

    // window 6 part 1
    glBegin(GL_POLYGON);
    glColor3f(0.176f, 0.537f, 0.729f);
    glVertex2f(23.6f, 7.6f);
    glVertex2f(24.4f, 6.8f);
    glVertex2f(24.4f, 7.6f);
    glEnd();

    // window 6 part 2
    glBegin(GL_POLYGON);
    glColor3f(0.529, 0.961, 0.992);
    glVertex2f(23.6f, 7.6f);
    glVertex2f(24.4f, 6.8f);
    glVertex2f(23.6f, 6.8f);
    glEnd();
}

// footpath left
void footpath(){
   glLineWidth(1);
    // floor
    glBegin(GL_POLYGON);
    glColor3f(0.67f, 0.81f, 0.65f);
    glVertex2f(0, 3);
    glVertex2f(0, 5);
    glVertex2f(14.7f, 5.0f);
    glVertex2f(14.5, 3.0f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(0, 3);
    glVertex2f(0, 5);
    glVertex2f(14.7f, 5.0f);
    glVertex2f(14.5, 3.0f);
    glEnd();

    // floor upper
    glBegin(GL_POLYGON);
    glColor3f(0.67f, 0.81f, 0.65f);
    glVertex2f(10, 9);
    glVertex2f(14.99f, 8.99f);
    glVertex2f(14.75f, 5.56f);
    glVertex2f(10.0f, 5.55f);
    glEnd();

    // afte9r
    glBegin(GL_POLYGON);
    glColor3f(0.67f, 0.81f, 0.65f);
    glVertex2f(14.75f, 5.56f);
    glVertex2f(14.67f, 5.0f);
    glVertex2f(9.4f, 4.99f);
    glVertex2f(10.0f, 5.55f);
    glEnd();

    // black dot left 1*
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(10.8f, 7.6f);
    glVertex2f(11.4f, 7.6f);
    glVertex2f(11.4f, 7.2f);
    glVertex2f(10.8f, 7.2f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(10.8f, 7.6f);
    glVertex2f(11.4f, 7.6f);
    glVertex2f(11.4f, 7.2f);
    glVertex2f(10.8f, 7.2f);
    glEnd();

    // black dot left 2
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(12.8f, 7.6f);
    glVertex2f(13.4f, 7.6f);
    glVertex2f(13.4f, 7.2f);
    glVertex2f(12.8f, 7.2f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(12.8f, 7.6f);
    glVertex2f(13.4f, 7.6f);
    glVertex2f(13.4f, 7.2f);
    glVertex2f(12.8f, 7.2f);
    glEnd();

    // black dot left 1
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(0.8f, 4.2f);
    glVertex2f(1.2f, 4.2f);
    glVertex2f(1.2f, 3.8f);
    glVertex2f(0.8, 3.8f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(0.8f, 4.2f);
    glVertex2f(1.2f, 4.2f);
    glVertex2f(1.2f, 3.8f);
    glVertex2f(0.8, 3.8f);
    glEnd();

    // black dot left 2
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(2.4f, 4.2f);
    glVertex2f(2.8f, 4.2f);
    glVertex2f(2.8f, 3.8f);
    glVertex2f(2.4f, 3.8f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(2.4f, 4.2f);
    glVertex2f(2.8f, 4.2f);
    glVertex2f(2.8f, 3.8f);
    glVertex2f(2.4f, 3.8f);
    glEnd();

    // black dot left 3
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(4.4f, 4.2f);
    glVertex2f(4.8f, 4.2f);
    glVertex2f(4.8f, 3.8f);
    glVertex2f(4.4f, 3.8f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(4.4f, 4.2f);
    glVertex2f(4.8f, 4.2f);
    glVertex2f(4.8f, 3.8f);
    glVertex2f(4.4f, 3.8f);
    glEnd();

    // black dot left 4
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(6.0f, 4.2f);
    glVertex2f(6.4f, 4.2f);
    glVertex2f(6.4f, 3.8f);
    glVertex2f(6.0f, 3.8f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(6.0f, 4.2f);
    glVertex2f(6.4f, 4.2f);
    glVertex2f(6.4f, 3.8f);
    glVertex2f(6.0f, 3.8f);
    glEnd();

    // black dot left 5
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(7.8f, 4.2f);
    glVertex2f(8.2f, 4.2f);
    glVertex2f(8.2f, 3.8f);
    glVertex2f(7.8f, 3.8f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(7.8f, 4.2f);
    glVertex2f(8.2f, 4.2f);
    glVertex2f(8.2f, 3.8f);
    glVertex2f(7.8f, 3.8f);
    glEnd();

    // black dot left 6
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(9.6f, 4.2f);
    glVertex2f(10.0f, 4.2f);
    glVertex2f(10.0f, 3.8f);
    glVertex2f(9.6f, 3.8f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(9.6f, 4.2f);
    glVertex2f(10.0f, 4.2f);
    glVertex2f(10.0f, 3.8f);
    glVertex2f(9.6f, 3.8f);
    glEnd();

    // black dot left 7
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(11.6f, 4.2f);
    glVertex2f(12.0f, 4.2f);
    glVertex2f(12.0f, 3.8f);
    glVertex2f(11.6f, 3.8f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(11.6f, 4.2f);
    glVertex2f(12.0f, 4.2f);
    glVertex2f(12.0f, 3.8f);
    glVertex2f(11.6f, 3.8f);
    glEnd();

    // black dot left 8
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(13.4f, 4.2f);
    glVertex2f(13.8f, 4.2f);
    glVertex2f(13.8f, 3.8f);
    glVertex2f(13.4f, 3.8f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(13.4f, 4.2f);
    glVertex2f(13.8f, 4.2f);
    glVertex2f(13.8f, 3.8f);
    glVertex2f(13.4f, 3.8f);
    glEnd();

    // tree 1 base
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(12.32f, 3.0f);
    glVertex2f(12.4f, 3.1f);
    glVertex2f(12.6f, 3.1f);
    glVertex2f(12.7f, 3.0f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(12.32f, 3.0f);
    glVertex2f(12.4f, 3.1f);
    glVertex2f(12.6f, 3.1f);
    glVertex2f(12.7f, 3.0f);
    glEnd();

    // tree 1 dal
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(12.5f, 3.1f);
    glVertex2f(12.6f, 3.1f);
    glVertex2f(12.5f, 3.8f);
    glVertex2f(12.6f, 3.8f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(12.5f, 3.1f);
    glVertex2f(12.55f, 3.1f);
    glVertex2f(12.5f, 3.9f);
    glEnd();

    // tree 1 pata
    glBegin(GL_POLYGON);
    glColor3f(0.24f, 0.50f, 0.18f);
    glVertex2f(12.4f, 3.9f);
    glVertex2f(12.6f, 3.9f);
    glVertex2f(12.8f, 3.8f);
    glVertex2f(12.2f, 3.8f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(12.4f, 3.9f);
    glVertex2f(12.6f, 3.9f);
    glVertex2f(12.8f, 3.8f);
    glVertex2f(12.2f, 3.8f);
    glEnd();

    // tree 1 pata 2
    glBegin(GL_POLYGON);
    glColor3f(0.24f, 0.50f, 0.18f);
    glVertex2f(12.4f, 4.04f);
    glVertex2f(12.64f, 4.04f);
    glVertex2f(12.76f, 3.88f);
    glVertex2f(12.26f, 3.88f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(12.4f, 4.04f);
    glVertex2f(12.64f, 4.04f);
    glVertex2f(12.76f, 3.88f);
    glVertex2f(12.26f, 3.88f);
    glEnd();

    // tree 1 pata 3
    glBegin(GL_POLYGON);
    glColor3f(0.24f, 0.50f, 0.18f);
    glVertex2f(12.76f, 4.02f);
    glVertex2f(12.27f, 4.01f);
    glVertex2f(12.53f, 4.3f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(12.76f, 4.02f);
    glVertex2f(12.27f, 4.01f);
    glVertex2f(12.53f, 4.3f);
    glEnd();

    // tree 2 base
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(8.7f, 3.1f);
    glVertex2f(8.9f, 3.1f);
    glVertex2f(9.0f, 3.0f);
    glVertex2f(8.6f, 3.0f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(8.7f, 3.1f);
    glVertex2f(8.9f, 3.1f);
    glVertex2f(9.0f, 3.0f);
    glVertex2f(8.6f, 3.0f);
    glEnd();

    // tree 2 dal
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(8.8f, 3.1f);
    glVertex2f(8.84f, 3.1f);
    glVertex2f(8.8f, 3.8f);
    glVertex2f(8.77f, 3.8f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(8.8f, 3.1f);
    glVertex2f(8.84f, 3.1f);
    glVertex2f(8.8f, 3.8f);
    glVertex2f(8.77f, 3.8f);
    glEnd();

    // tree 2 pata 1
    glBegin(GL_POLYGON);
    glColor3f(0.24f, 0.50f, 0.18f);
    glVertex2f(8.55f, 3.6f);
    glVertex2f(9.1f, 3.6f);
    glVertex2f(8.8f, 3.8f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(8.55f, 3.6f);
    glVertex2f(9.1f, 3.6f);
    glVertex2f(8.8f, 3.8f);
    glEnd();

    // tree 2 pata 2
    glBegin(GL_POLYGON);
    glColor3f(0.24f, 0.50f, 0.18f);
    glVertex2f(8.55f, 3.75f);
    glVertex2f(9.1f, 3.75f);
    glVertex2f(8.8f, 4.0f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(8.55f, 3.75f);
    glVertex2f(9.1f, 3.75f);
    glVertex2f(8.8f, 4.0f);
    glEnd();

    // tree 2 pata 3
    glBegin(GL_POLYGON);
    glColor3f(0.24f, 0.50f, 0.18f);
    glVertex2f(8.55f, 3.9f);
    glVertex2f(9.1f, 3.9f);
    glVertex2f(8.8f, 4.2f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(8.55f, 3.9f);
    glVertex2f(9.1f, 3.9f);
    glVertex2f(8.8f, 4.2f);
    glEnd();

    // tree 3 base
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(5.12f, 3.1f);
    glVertex2f(5.28f, 3.1f);
    glVertex2f(5.34f, 3.0f);
    glVertex2f(5.03f, 3.0f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(5.12f, 3.1f);
    glVertex2f(5.28f, 3.1f);
    glVertex2f(5.34f, 3.0f);
    glVertex2f(5.03f, 3.0f);
    glEnd();

    // tree 3 dal
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(5.2f, 3.1f);
    glVertex2f(5.23f, 3.1f);
    glVertex2f(5.21f, 3.68f);
    glVertex2f(5.18f, 3.67f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(5.2f, 3.1f);
    glVertex2f(5.23f, 3.1f);
    glVertex2f(5.21f, 3.68f);
    glVertex2f(5.18f, 3.67f);
    glEnd();

    // tree 3 pata 1
    glBegin(GL_POLYGON);
    glColor3f(0.24f, 0.50f, 0.18f);
    glVertex2f(5.0f, 3.6f);
    glVertex2f(5.4f, 3.6f);
    glVertex2f(5.2f, 3.8f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(5.0f, 3.6f);
    glVertex2f(5.4f, 3.6f);
    glVertex2f(5.2f, 3.8f);
    glEnd();

    // tree 3 pata 2
    glBegin(GL_POLYGON);
    glColor3f(0.24f, 0.50f, 0.18f);
    glVertex2f(5.0f, 3.7f);
    glVertex2f(5.4f, 3.72f);
    glVertex2f(5.2f, 3.9f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(5.0f, 3.7f);
    glVertex2f(5.4f, 3.72f);
    glVertex2f(5.2f, 3.9f);
    glEnd();

    // tree 3 pata 3
    glBegin(GL_POLYGON);
    glColor3f(0.24f, 0.50f, 0.18f);
    glVertex2f(5.0f, 3.8f);
    glVertex2f(5.36f, 3.84f);
    glVertex2f(5.2f, 4.0f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(5.0f, 3.8f);
    glVertex2f(5.36f, 3.84f);
    glVertex2f(5.2f, 4.0f);
    glEnd();

    // tree 4 base
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(1.52f, 3.14f);
    glVertex2f(1.72f, 3.14f);
    glVertex2f(1.8f, 3.0f);
    glVertex2f(1.44f, 3.0f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(1.52f, 3.14f);
    glVertex2f(1.72f, 3.14f);
    glVertex2f(1.8f, 3.0f);
    glVertex2f(1.44f, 3.0f);
    glEnd();

    // tree 4 dal
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(1.6f, 3.14f);
    glVertex2f(1.663f, 3.14f);
    glVertex2f(1.65f, 3.8f);
    glVertex2f(1.6f, 3.78f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(1.6f, 3.14f);
    glVertex2f(1.663f, 3.14f);
    glVertex2f(1.65f, 3.8f);
    glVertex2f(1.6f, 3.78f);
    glEnd();

    // tree 4 pata 1
    glBegin(GL_POLYGON);
    glColor3f(0.24f, 0.50f, 0.18f);
    glVertex2f(1.35f, 3.63f);
    glVertex2f(1.96f, 3.63f);
    glVertex2f(1.62f, 3.89f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(1.35f, 3.63f);
    glVertex2f(1.96f, 3.63f);
    glVertex2f(1.62f, 3.89f);
    glEnd();

    // tree 4 pata 2
    glBegin(GL_POLYGON);
    glColor3f(0.24f, 0.50f, 0.18f);
    glVertex2f(1.4f, 3.8f);
    glVertex2f(1.9f, 3.8f);
    glVertex2f(1.63f, 4.0f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(1.4f, 3.8f);
    glVertex2f(1.9f, 3.8f);
    glVertex2f(1.63f, 4.0f);
    glEnd();

    // tree 4 pata 3
    glBegin(GL_POLYGON);
    glColor3f(0.24f, 0.50f, 0.18f);
    glVertex2f(1.4f, 3.9f);
    glVertex2f(1.9f, 3.9f);
    glVertex2f(1.6f, 4.2f);
    glEnd();

    // Black border
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glBegin(GL_LINE_LOOP);
    glVertex2f(1.4f, 3.9f);
    glVertex2f(1.9f, 3.9f);
    glVertex2f(1.6f, 4.2f);
    glEnd();

    // seat
    // upper line 1
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(10.8f, 6.13f);
    glVertex2f(11.6f, 6.13f);
    glVertex2f(11.6f, 6.0f);
    glVertex2f(10.8f, 6.0f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(10.8f, 6.13f);
    glVertex2f(11.6f, 6.13f);
    glVertex2f(11.6f, 6.0f);
    glVertex2f(10.8f, 6.0f);
    glEnd();

    // upper line 2
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(10.8f, 5.9f);
    glVertex2f(11.6f, 5.9f);
    glVertex2f(11.6f, 5.75f);
    glVertex2f(10.8f, 5.75f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(10.8f, 5.9f);
    glVertex2f(11.6f, 5.9f);
    glVertex2f(11.6f, 5.75f);
    glVertex2f(10.8f, 5.75f);
    glEnd();

    // upper line 3
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(10.6f, 5.6f);
    glVertex2f(11.68f, 5.6f);
    glVertex2f(11.9f, 5.32f);
    glVertex2f(10.45f, 5.30f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(10.6f, 5.6f);
    glVertex2f(11.68f, 5.6f);
    glVertex2f(11.9f, 5.32f);
    glVertex2f(10.45f, 5.30f);
    glEnd();

    // upper middle 1
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(10.95f, 5.6f);
    glVertex2f(11.0f, 6.0f);
    glVertex2f(11.0f, 6.0f);
    glVertex2f(10.95f, 5.6f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(10.95f, 5.6f);
    glVertex2f(11.0f, 6.0f);
    glVertex2f(11.0f, 6.0f);
    glVertex2f(10.95f, 5.6f);
    glEnd();

    // upper middle 2
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(11.35f, 6.0f);
    glVertex2f(11.4f, 6.0f);
    glVertex2f(11.4f, 5.87f);
    glVertex2f(11.35f, 5.87f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(11.35f, 6.0f);
    glVertex2f(11.4f, 6.0f);
    glVertex2f(11.4f, 5.87f);
    glVertex2f(11.35f, 5.87f);
    glEnd();

    // upper middle 4
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(11.35f, 5.75f);
    glVertex2f(11.4f, 5.75f);
    glVertex2f(11.4f, 5.6f);
    glVertex2f(11.35f, 5.6f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(11.35f, 5.75f);
    glVertex2f(11.4f, 5.75f);
    glVertex2f(11.4f, 5.6f);
    glVertex2f(11.35f, 5.6f);
    glEnd();

    // paya 1
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(10.7f, 5.3f);
    glVertex2f(10.4f, 5.0f);
    glVertex2f(10.6f, 5.3f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(10.7f, 5.3f);
    glVertex2f(10.4f, 5.0f);
    glVertex2f(10.6f, 5.3f);
    glEnd();

    // paya 2
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(10.86f, 5.3f);
    glVertex2f(10.96f, 5.31f);
    glVertex2f(10.84f, 5.1f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(10.86f, 5.3f);
    glVertex2f(10.96f, 5.31f);
    glVertex2f(10.84f, 5.1f);
    glEnd();

    // paya 3
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(11.4f, 5.3f);
    glVertex2f(11.5f, 5.32f);
    glVertex2f(11.6f, 5.1f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(11.4f, 5.3f);
    glVertex2f(11.5f, 5.32f);
    glVertex2f(11.6f, 5.1f);
    glEnd();

    // paya 4
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(11.6f, 5.3f);
    glVertex2f(11.7f, 5.3f);
    glVertex2f(11.9f, 5.0f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(11.6f, 5.3f);
    glVertex2f(11.7f, 5.3f);
    glVertex2f(11.9f, 5.0f);
    glEnd();

    // tree 5 pata 1 upper left
    // base
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(13.29f, 5.2f);
    glVertex2f(13.6f, 5.2f);
    glVertex2f(13.8f, 4.9f);
    glVertex2f(13.13f, 4.9f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(13.29f, 5.2f);
    glVertex2f(13.6f, 5.2f);
    glVertex2f(13.8f, 4.9f);
    glVertex2f(13.13f, 4.9f);
    glEnd();

    // tree 5 dal
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(13.4f, 6.2f);
    glVertex2f(13.54f, 6.2f);
    glVertex2f(13.55f, 5.2f);
    glVertex2f(13.4f, 5.2f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(13.4f, 6.2f);
    glVertex2f(13.54f, 6.2f);
    glVertex2f(13.55f, 5.2f);
    glVertex2f(13.4f, 5.2f);
    glEnd();

    // tree 5 pata 1
    glBegin(GL_POLYGON);
    glColor3f(0.24f, 0.50f, 0.18f);
    glVertex2f(13.0f, 6.0f);
    glVertex2f(14.07f, 6.0f);
    glVertex2f(13.5f, 6.5f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(13.0f, 6.0f);
    glVertex2f(14.07f, 6.0f);
    glVertex2f(13.5f, 6.5f);
    glEnd();

    // tree 5 pata 2
    glBegin(GL_POLYGON);
    glColor3f(0.24f, 0.50f, 0.18f);
    glVertex2f(13.0f, 6.3f);
    glVertex2f(14.0f, 6.3f);
    glVertex2f(13.5f, 6.7f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(13.0f, 6.3f);
    glVertex2f(14.0f, 6.3f);
    glVertex2f(13.5f, 6.7f);
    glEnd();

    // tree 5 pata 3
    glBegin(GL_POLYGON);
    glColor3f(0.24f, 0.50f, 0.18f);
    glVertex2f(14.0f, 6.5f);
    glVertex2f(13.1f, 6.5f);
    glVertex2f(13.5f, 7.1f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(14.0f, 6.5f);
    glVertex2f(13.1f, 6.5f);
    glVertex2f(13.5f, 7.1f);
    glEnd();

    // left upper floor
    // floor upper
    glBegin(GL_POLYGON);
    glColor3f(0.67f, 0.81f, 0.65f);
    glVertex2f(17.2f, 9);
    glVertex2f(21.4f, 9.0f);
    glVertex2f(21.4f, 4.99f);
    glVertex2f(17.54f, 4.99f);
    glEnd();

    // afte9r
    glBegin(GL_POLYGON);
    glColor3f(0.67f, 0.81f, 0.65f);
    glVertex2f(17.54f, 4.99f);
    glVertex2f(30.0f, 5.0f);
    glVertex2f(30.0f, 3.0f);
    glVertex2f(17.5f, 3.0f);
    glEnd();

    // black dot left 1* upper
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(18.0f, 7.6f);
    glVertex2f(18.6f, 7.6f);
    glVertex2f(18.6f, 7.0f);
    glVertex2f(18.0f, 7.03f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(18.0f, 7.6f);
    glVertex2f(18.6f, 7.6f);
    glVertex2f(18.6f, 7.0f);
    glVertex2f(18.0f, 7.03f);
    glEnd();

    // black dot  2 left 1* upper
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(19.8f, 7.6f);
    glVertex2f(20.4f, 7.6f);
    glVertex2f(20.4f, 7.0f);
    glVertex2f(19.8f, 7.0f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(19.8f, 7.6f);
    glVertex2f(20.4f, 7.6f);
    glVertex2f(20.4f, 7.0f);
    glVertex2f(19.8f, 7.0f);
    glEnd();

    // tree 6 base
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(23.5f, 3.2f);
    glVertex2f(23.9f, 3.2f);
    glVertex2f(23.99f, 3.0f);
    glVertex2f(23.45f, 3.0f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(23.5f, 3.2f);
    glVertex2f(23.9f, 3.2f);
    glVertex2f(23.99f, 3.0f);
    glVertex2f(23.45f, 3.0f);
    glEnd();

    // tree 4 dal
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(23.8f, 3.2f);
    glVertex2f(23.7f, 3.9f);
    glVertex2f(23.7f, 3.2f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(23.8f, 3.2f);
    glVertex2f(23.7f, 3.9f);
    glVertex2f(23.7f, 3.2f);
    glEnd();

    // tree 4 pata 1
    glBegin(GL_POLYGON);
    glColor3f(0.24f, 0.50f, 0.18f);
    glVertex2f(23.3f, 3.65f);
    glVertex2f(24.1f, 3.65f);
    glVertex2f(23.7f, 4.1f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(23.3f, 3.65f);
    glVertex2f(24.1f, 3.65f);
    glVertex2f(23.7f, 4.1f);
    glEnd();

    // tree 4 pata 1
    glBegin(GL_POLYGON);
    glColor3f(0.24f, 0.50f, 0.18f);
    glVertex2f(24.1f, 3.9f);
    glVertex2f(23.34f, 3.84f);
    glVertex2f(23.7f, 4.35f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(24.1f, 3.9f);
    glVertex2f(23.34f, 3.84f);
    glVertex2f(23.7f, 4.35f);
    glEnd();

    // black dot  2 left 1* upper
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(19.8f, 4.2f);
    glVertex2f(21.0f, 4.2f);
    glVertex2f(21.0f, 3.8f);
    glVertex2f(19.8f, 3.8f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(19.8f, 4.2f);
    glVertex2f(21.0f, 4.2f);
    glVertex2f(21.0f, 3.8f);
    glVertex2f(19.8f, 3.8f);
    glEnd();

    // black dot  2 left 1* upper
    glBegin(GL_POLYGON);
    glColor3f(0.396f, 0.263f, 0.129f);
    glVertex2f(26.4f, 4.2f);
    glVertex2f(27.2f, 4.2f);
    glVertex2f(27.2f, 3.8f);
    glVertex2f(26.4f, 3.8f);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(26.4f, 4.2f);
    glVertex2f(27.2f, 4.2f);
    glVertex2f(27.2f, 3.8f);
    glVertex2f(26.4f, 3.8f);
    glEnd();

}
// Cloud movement variables
float cloud1_x = 0.0;
float cloud2_x = 0.0;
float speed = 0.05;

void dima_cloud1()
{
    glPushMatrix();
    glTranslatef(cloud1_x, 0, 0); // Move cloud1

    // Original cloud1 drawing
    glBegin(GL_POLYGON);
    glColor3f(1, 1, 1);
    glVertex2f(3.5, 13);
    glVertex2f(5.5, 13);
    glVertex2f(5.5, 14);
    glVertex2f(3.5, 14);
    glEnd();

    drawFilledCircle(3.3994933168948, 13.704422303866, 0.7, 100);
    drawFilledCircle(4.2471012323834, 13.946595994005, 0.77, 100);
    drawFilledCircle(5.3368828380116, 13.6817185204154, 0.68, 100);

    glPopMatrix();
}

void dima_cloud2()
{
    glPushMatrix();
    glTranslatef(cloud2_x, 0, 0);

    // Original cloud2 drawing
    glBegin(GL_POLYGON);
    glColor3f(1, 1, 1);
    glVertex2f(25, 13);
    glVertex2f(28, 13);
    glVertex2f(28, 14.2);
    glVertex2f(25, 14.2);
    glEnd();

    drawFilledCircle(25.208894603203, 13.752524655295, 0.76, 100);
    drawFilledCircle(26.4359625127227, 13.8993031133711, 0.9, 100);
    drawFilledCircle(27.8039377419959, 13.758395793618, 0.77, 100);

    glPopMatrix();
}
void cloud_update(int value)
{
    cloud1_x += speed;
    cloud2_x -= speed;


    if (cloud1_x > 30)
    {
        cloud1_x = -5;
    }


    if (cloud2_x < -30)
    {
        cloud2_x = 5;
    }

    glutPostRedisplay();
   // glutTimerFunc(20, cloud_update, 0);
}



void sun()
{
    drawFilledCircle(16, 13,1,100);
}






void house_clock(float rotationAngle) {
    // Clock body
    glColor3f(1, 1, 1);
    drawFilledCircle(4.9076349178553, 10.6545629615035, 0.8, 100);
    glColor3f(0, 0, 0);
    drawHollowCircle(4.9076349178553, 10.6545629615035, 0.8, 100);

    // 12 o'clock
    glBegin(GL_POLYGON);
    glColor3f(0, 0, 0);
    glVertex2f(4.9f, 11.45f);
    glVertex2f(4.95f, 11.45f);
    glVertex2f(4.95, 11.25);
    glVertex2f(4.9, 11.25);
    glEnd();

    // 3 o'clock
    glBegin(GL_POLYGON);
    glColor3f(0, 0, 0);
    glVertex2f(5.45, 10.7);
    glVertex2f(5.7, 10.7);
    glVertex2f(5.7, 10.65);
    glVertex2f(5.45, 10.65);
    glEnd();

    // 6 o'clock
    glBegin(GL_POLYGON);
    glColor3f(0, 0, 0);
    glVertex2f(4.9, 10.05);
    glVertex2f(4.95, 10.05);
    glVertex2f(4.95, 9.86);
    glVertex2f(4.9, 9.86);
    glEnd();

    // 9 o'clock
    glBegin(GL_POLYGON);
    glColor3f(0, 0, 0);
    glVertex2f(4.35, 10.7);
    glVertex2f(4.35, 10.65);
    glVertex2f(4.11, 10.65);
    glVertex2f(4.1136, 10.7);
    glEnd();

    // Clock middle circle
    drawFilledCircle(4.9076349178553, 10.6545629615035, 0.05, 100);

    // Hour line
    glBegin(GL_POLYGON);
    glColor3f(0, 0, 0);
    glVertex2f(5.06, 10.94);
    glVertex2f(5.11, 10.91);
    glVertex2f(4.95, 10.68);
    glVertex2f(4.9, 10.71);
    glEnd();

    // Rotate the minute line
    glPushMatrix();
    glTranslatef(4.9076349178553, 10.6545629615035, 0);
    glRotatef(rotationAngle, 0, 0, 1);
    glTranslatef(-4.9076349178553, -10.6545629615035, 0);

    // Minute line
    glBegin(GL_POLYGON);
    glColor3f(0, 0, 0);
    glVertex2f(4.88, 10.64);
    glVertex2f(4.94, 10.64);
    glVertex2f(4.94, 10.14);
    glVertex2f(4.88, 10.14);
    glEnd();

    glPopMatrix();
}




int currentLight = 0; // 0 = Green, 1 = Yellow, 2 = Red



// Function to draw the traffic light
void traffic_light() {
    // Base
    glBegin(GL_POLYGON);
    glColor3f(0, 0, 0);
    glVertex2f(18, 3);
    glVertex2f(18.6, 3);
    glVertex2f(18.6, 3.4);
    glVertex2f(18, 3.4);
    glEnd();

    // Stem
    glBegin(GL_POLYGON);
    glColor3f(0.286, 0.298, 0.388);
    glVertex2f(18.15, 3.4);
    glVertex2f(18.45, 3.4);
    glVertex2f(18.45, 4.35);
    glVertex2f(18.15, 4.35);
    glEnd();

    // Stem upper
    glBegin(GL_POLYGON);
    glColor3f(0.286, 0.298, 0.388);
    glVertex2f(18.25, 4.35);
    glVertex2f(18.35, 4.35);
    glVertex2f(18.35, 6.05);
    glVertex2f(18.25, 6.05);
    glEnd();

    // Light boxes
    glBegin(GL_POLYGON);
    glColor3f(0, 0, 0);
    glVertex2f(18.35,4.55);
    glVertex2f(18.5, 4.55);
    glVertex2f(18.5, 4.9);
    glVertex2f(18.35, 4.9);
    glEnd();

    glBegin(GL_POLYGON);
    glColor3f(0, 0, 0);
    glVertex2f(18.35, 5.12);
    glVertex2f(18.5, 5.12);
    glVertex2f(18.5, 5.45);
    glVertex2f(18.35, 5.45);
    glEnd();

    glBegin(GL_POLYGON);
    glColor3f(0, 0, 0);
    glVertex2f(18.35, 5.6);
    glVertex2f(18.5, 5.6);
    glVertex2f(18.5, 5.95);
    glVertex2f(18.35, 5.95);
    glEnd();

    // Change traffic light based on the currentLight variable
    if (currentLight == 0) {
        glColor3f(0.0, 1.0, 0.0); // Green
    } else {
        glColor3f(0.2, 0.2, 0.2);
    }
  drawRightSemiCircle(18.5, 5.78, 0.13, 100);

    if (currentLight == 1) {
        glColor3f(1.0, 1.0, 0.0); // Yellow
    } else {
        glColor3f(0.2, 0.2, 0.2);
    }
   drawRightSemiCircle(18.5, 5.28, 0.13, 100);

    if (currentLight == 2) {
        glColor3f(1.0, 0.0, 0.0); // Red
    } else {
        glColor3f(0.2, 0.2, 0.2);
    }
  drawRightSemiCircle(18.5, 4.72, 0.14, 100);
}





void dima_mouseClick(int button, int state, int x, int y) {
    if (button == GLUT_LEFT_BUTTON && state == GLUT_DOWN) {
        currentLight = 0; // Green Light
        glutPostRedisplay();
    }
}

// Keyboard function
void dima_keyboard(unsigned char key, int x, int y) {
    if (key == 'r' || key == 'R') {
        currentLight = 2; // Red Light
    } else if (key == 'y' || key == 'Y') {
        currentLight = 1; // Yellow Light
    }
    glutPostRedisplay();
}












float carPositionX = 0.0f;


void car() {
    glPushMatrix();
    glTranslatef(carPositionX, 0.0f, 0.0f);

    glLineWidth(1);

    // In box
    glBegin(GL_POLYGON);
    glColor3f(0.969, 0.914, 0.314);
    glVertex2f(26.6, 2.6);
    glVertex2f(29.4, 2.6);
    glVertex2f(29.4066987989381, 1.8505326347374);
    glVertex2f(26.6, 1.84);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(26.6, 2.6);
    glVertex2f(29.4, 2.6);
    glVertex2f(29.4066987989381, 1.8505326347374);
    glVertex2f(26.6, 1.84);
    glEnd();

    // Box
    glBegin(GL_POLYGON);
    glColor3f(0.973, 0.592, 0.235);
    glVertex2f(26.5998380675157, 1.8428308585161);
    glVertex2f(29.4066987989381, 1.8505326347374);
    glVertex2f(29.4, 2.5);
    glVertex2f(26.6, 2.5);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(27.5, 3.1);
    glVertex2f(27.28, 2.6);
    glVertex2f(27, 2.6);
    glEnd();

    // Wheel
    glColor3f(0, 0, 0);
    drawFilledCircle(28.8227111161263, 1.8634710132301, 0.324, 100);
    drawFilledCircle(27.1941395360478, 1.8488211923149, 0.324, 100);

    // Middle wheel
    glColor3f(1, 1, 1);
    drawFilledCircle(28.8227111161263, 1.8634710132301, 0.16, 100);
    drawFilledCircle(27.1941395360478, 1.8488211923149, 0.16, 100);

    // Front headlight
    glBegin(GL_POLYGON);
    glColor3f(0, 0, 0);
    glVertex2f(26.46, 1.92);
    glVertex2f(26.6, 1.92);
    glVertex2f(26.6, 1.84);
    glVertex2f(26.46, 1.84);
    glEnd();

    glBegin(GL_POLYGON);
    glColor3f(0.973, 0.592, 0.235);
    glVertex2f(26.5, 2.04);
    glVertex2f(26.6, 2.04);
    glVertex2f(26.6, 1.92);
    glVertex2f(26.5, 1.92);
    glEnd();

    // Handle
    glBegin(GL_POLYGON);
    glColor3f(0, 0, 0);
    glVertex2f(27.6, 2.55);
    glVertex2f(27.8, 2.55);
    glVertex2f(27.8, 2.5);
    glVertex2f(27.6, 2.5);
    glEnd();

    // Roof
    glBegin(GL_POLYGON);
    glColor3f(0.969, 0.914, 0.314);
    glVertex2f(27.5, 3.1);
    glVertex2f(28.6, 3.1);
    glVertex2f(28.6, 2.9);
    glVertex2f(27.4, 2.9);
    glEnd();

    // Front window
    glBegin(GL_POLYGON);
    glColor3f(0.208, 0.804, 0.965);
    glVertex2f(27.5, 3.1);
    glVertex2f(27.28, 2.6);
    glVertex2f(27, 2.6);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(27.5, 3.1);
    glVertex2f(27.28, 2.6);
    glVertex2f(27, 2.6);
    glEnd();

    glBegin(GL_POLYGON);
    glColor3f(0.208, 0.804, 0.965);
    glVertex2f(27.4, 2.9);
    glVertex2f(28, 2.9);
    glVertex2f(28, 2.6);
    glVertex2f(27.28, 2.6);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(27.4, 2.9);
    glVertex2f(28, 2.9);
    glVertex2f(28, 2.6);
    glVertex2f(27.28, 2.6);
    glEnd();

    glBegin(GL_POLYGON);
    glColor3f(0.208, 0.804, 0.965);
    glVertex2f(28, 2.9);
    glVertex2f(28.6, 2.9);
    glVertex2f(28.6, 2.6);
    glVertex2f(28, 2.6);
    glEnd();

    // Black border
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(28, 2.9);
    glVertex2f(28.6, 2.9);
    glVertex2f(28.6, 2.6);
    glVertex2f(28, 2.6);
    glEnd();

    // Line
    glBegin(GL_LINE_LOOP);
    glColor3f(0.0f, 0.0f, 0.0f); // Black color
    glVertex2f(28, 2.6);
    glVertex2f(28, 1.84);
    glEnd();

    glPopMatrix();
}

void car_update(int value) {
    float stopPosition = -8.0f;


    if (currentLight == 2 && carPositionX <= stopPosition) {
        carPositionX = stopPosition;
    } else {
        // Move the car normally if the light is not red
        carPositionX -= 0.1f;

        // If the car goes off-screen, reset its position
        if (carPositionX < -30.0f) {
            carPositionX = 10.0f;
        }
    }

    glutPostRedisplay();

}









void gradient_background_dima()
{
    glBegin(GL_QUADS);

    // Bottom color (White)
    glColor3f(1.0f, 1.0f, 1.0f);
    glVertex2f(0, 0);
    glVertex2f(30, 0);

    // Top color (Light Blue)
    glColor3f(0.678f, 0.847f, 0.902f); // Light blue color (sky blue)
    glVertex2f(30, 15);
    glVertex2f(0, 15);

    glEnd();
}

void dima_display()
{
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
    gradient_background_dima();
    // first_road();

    first_left_middle_road();

    // first_box();

    dima_first_house();
    dima_second_house();

    third_house();
    forth_house();
    fifth_house();
    footpath();
    dima_cloud1();
    dima_cloud2();
    sun();
     static float angle = 0.0f;
    house_clock(angle);

    angle += -0.2f;
    if (angle >= 360.0f)
        {
        angle = 0.0f;
    }


    traffic_light();
    car();

        carPositionX -= 0.1f;
    if (carPositionX < -30.0f)
        {

        carPositionX = 10.0f;
    }

    glFlush();
}



////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////


//main update
void update(int value) {
    UpdateRightBallonPos();
    UpdateLeftBallonPos();
    // UpdateLeftLeaf();
   // UpdateBallpos();


    UpdateJuiceSign(value);
    updateS4_Stars(value);
    UpdateWave1();
    UpdateWave2();
    UpdateWave3();
    S4_UpdateLightBeam(value);
    S4_sandcastle_flag_update(value);
    flame_update(value);
   cloud_update(value);
   fruit_timer(value);
   car_update(value);

    //
    if (s2_isMoving)
    {
        // Update cloud positions
        s2_cloud1X += s2_cloudSpeed;
        s2_cloud2X += s2_cloudSpeed;
        s2_cloud3X += s2_cloudSpeed;
        s2_cloud4X += s2_cloudSpeed;
        s2_cloud5X += s2_cloudSpeed;

        // Reset clouds when they move off the screen
        if (s2_cloud1X > 30.0f) s2_cloud1X = -1.0f;
        if (s2_cloud2X > 30.0f) s2_cloud2X = -1.0f;
        if (s2_cloud3X > 30.0f) s2_cloud3X = -1.0f;
        if (s2_cloud4X > 30.0f) s2_cloud4X = -1.0f;
        if (s2_cloud5X > 30.0f) s2_cloud5X = -1.0f;

        // Update bird positions
        s2_bird1X -= s2_birdSpeed;
        s2_bird2X -= s2_birdSpeed;

        // Wing flapping animation
        s2_wingFlap += 0.1f;
        if (s2_wingFlap > 2 * 3.14159f) {
            s2_wingFlap = 0.0f;
        }

        if (s2_bird1X < -2.0f) s2_bird1X = 30.0f;
        if (s2_bird2X < -2.0f) s2_bird2X = 30.0f;

        // Duck movement
        if (s2_movingRight) {
            s2_duckX += s2_duckSpeed;
            if (s2_duckX > 5.0f) s2_movingRight = false;
        } else {
            s2_duckX -= s2_duckSpeed;
            if (s2_duckX < 0.0f) s2_movingRight = true;
        }

        // Boat movement
        if (s2_boatMovingLeft) {
            s2_boatX -= s2_boatSpeed;
            if (s2_boatX < 1.0f) s2_boatMovingLeft = false;
        } else {
            s2_boatX += s2_boatSpeed;
            if (s2_boatX > 21.0f) s2_boatMovingLeft = true;
        }

        // Water drop animation
        s2_waterY -= 0.05f;
        if (s2_waterY < 9.8f) s2_waterY = 10.00f;

        // Tube well handle rotation simulation
        if (s2_handleMovingForward) {
            s2_handleAngle += 2.0f;
            if (s2_handleAngle >= 410.0f) s2_handleMovingForward = false;
        } else {
            s2_handleAngle -= 1.0f;
            if (s2_handleAngle <= 380.0f) s2_handleMovingForward = true;
        }

        // Man movement (oscillating left and right)
        if (s2_manX <= 19.4f) {
            s2_manMovingRight = false;
        } else if (s2_manX >= 30.02f) {
            s2_manMovingRight = true;
        }
        s2_manX += s2_manMovingRight ? -s2_manSpeed : s2_manSpeed;

        // Kite movement
        s2_kiteY = 13.55f + s2_kiteAmplitude * sin(s2_kiteFrequency * glutGet(GLUT_ELAPSED_TIME));

             // Handle action reset timer
        if (s2_isActionTriggered) {
        s2_actionTimer -= 0.05f;
        if (s2_actionTimer <= 0.0f)
            {
            s2_isActionTriggered = false;  // Reset action flag after timer expires
            }
        }
    }



    //update_bus

    if (currentScene == 3 && busPosition >= 1.0f) {
        busStopped = true; // Stop the bus at a certain point in the first scene
    }
    if (!busStopped) {
            if(!isPaused){
        busPosition += 0.1f;

        wheelRotation -= 10.0f;
        if (wheelRotation <= -360.0f) {
            wheelRotation += 360.0f;
        }
        if (busPosition > 30.0f) {
            busPosition = 0.0f;
            currentScene++;
            if (currentScene > 3) {
                currentScene = 0;
            }
        }
    }
    }

    timer(value);



    glutPostRedisplay();
    glutTimerFunc(25, update, 50);
}





void f_keyboard(unsigned char key, int x, int y) {
    if (key == ' ') {  // Press 'p' to pause/resume
        isPaused = !isPaused;
        //cout << (isPaused ? "Paused\n" : "Resumed\n");
    }
}
void b_display() {
    glClearColor(0.7f, 1.0f, 1.0f, 1.0f);
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

    if (currentScene == 0){
        dima_display();
    }

    else if (currentScene == 1) {
        s2_display_ShowElement();
    } else if (currentScene == 2) {
        a_display();
    } else if (currentScene == 3) {
        display();
    }

    Bus();
    glFlush();

}

void unifiedMouse(int button, int state, int x, int y) {

    if (currentScene == 0) {
        dima_mouseClick(button, state, x, y);
    }

    else if (currentScene == 1) {
        s2_mouseCallback(button, state, x, y);
    } else if (currentScene == 2) {
        a_mouse(button, state, x, y);
    } else if (currentScene == 3) {
        ab_mouse(button, state, x, y);}
}

void unifiedKeyboard(unsigned char key, int x, int y) {

    if (currentScene == 0) {
        dima_keyboard(key, x, y);
    }

    else if (currentScene == 1) {
        s2_keyboard(key, x, y);
    } else if (currentScene == 2) {
        a_keyboard(key, x, y);
    } else if (currentScene == 3) {
        ab_keyboard(key, x, y);
    }


    if(currentScene == 0 || currentScene == 1 || currentScene ==2 ){
        f_keyboard(key,x,y);
    }
}





void myInit(void) {
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    gluOrtho2D(0.0, 30.0, 0.0, 15.0);
    glEnable(GL_LINE_SMOOTH);
    glEnable(GL_POLYGON_SMOOTH);
    glEnable(GL_BLEND);
    glBlendFunc(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA);
    glHint(GL_LINE_SMOOTH_HINT, GL_NICEST);
    glHint(GL_POLYGON_SMOOTH_HINT, GL_NICEST);
}

int main(int argc, char** argv) {
    glutInit(&argc, argv);
    glutInitWindowSize(1800, 900);
    glutInitWindowPosition(50, 50);
    glutCreateWindow("Scene four- Beachside view");
    glutDisplayFunc(b_display);
    //glutMouseFunc(s2_mouseCallback);  // Register Mouse Callback function
    //glutKeyboardFunc(s2_keyboard);  // Register keyboard function
    //glutMouseFunc(mouse);          // Mouse input for left click
    //glutKeyboardFunc(keyboard);    // Keyboard input for "r" key press
    glutMouseFunc(unifiedMouse);  // Unified mouse handler
    glutKeyboardFunc(unifiedKeyboard);  // Unified keyboard handler
    //glutKeyboardFunc(f_keyboard);
    glutTimerFunc(50, update, 0);
    myInit();
    glutMainLoop();
    return 0;
}

