---
title: Java에서 BufferedReader와 BufferWriter 사용하기
author: Hamin
date: 2020-09-30 16:48:28
tags: [Java]
categories:
- [Java]
thumbnail: /gallery/java.png
---

BufferedReader와 BufferedWriter은 이름처럼 버퍼를 이용해서 읽고 쓰는 함수입니다.
이 함수는 버퍼를 이용하기 때문에 이 함수를 이용하면 입출력의 효율이 비교할 수 없을 정도로 좋아집니다.

<!--more-->

{% asset_img buffer.png %}



한 번 거쳐가므로 느릴거 같은데 왜 빠를까요?
하드디스크는 원래 속도가 매우 느립니다.
하드디스크 뿐만 아니라 키보드나 모니터와 같은 외부 장치와의 데이터 입출력은 생각보다 시간이 걸리는 작업입니다.
버퍼링 없이 키보드가 눌릴 때마다 눌린 문자의 정보를 목적지로 바로 이동시키는 것이 보다 효율적이고 빠릅니다.
그냥 전송하게 되면 CPU의 성능 갭이 많이 나서 비효율적입니다.
<br>
<br>

예를 들자면, 흙을 퍼서 저 언덕에 버리는데, 한 번 삽질할 때마다 가서 버리는 것보다, 수레에 가득 채워서 한 번에 나르는 것이 효율적인것과 같은 것 입니다.
즉 모아뒀다가 한 번에 전송하는 것이 훨씬 효율적이라는 것입니다.

~~~markdown
# 버퍼 입출력

## 버퍼(buffer)
- 데이터를 한 곳에서 다른 한 곳으로 전송하는 동안 일시적으로 그 데이터를 보관하는 임시 메모리 영역
- 입출력 속도 향상을 위해 버퍼 사용

## 버퍼 플러시(buffer flush) : 버퍼에 남아 있는 데이터를 출력시킴(버퍼를 비움)

## 버퍼를 이용한 입력
- BufferedReader

## 버퍼를 이용한 출력
- BufferedWriter
~~~

# BufferedReader

자바를 사용하는 사람들은 보통 Scanner를 이용해 입력받아 사용하는데, 띄어쓰기(스페이스)와 엔터(개행문자)를 경계로 입력값을 인식하기 때문에 따로 가공할 필요가 없어서 사용하기 매우 편리합니다.
반면에 BufferedReader는 엔터만 경계로 인식하고 받은 데이터가 String으로 고정되기 때문에 데이터를 따로 가공해야 하는 경우가 많습니다.
그래소 다소 번거롭습니다. 하지만 <span style="color:red">Scanner에 비해서 상대적으로 빠릅니다.<span>

공식 문서에는 
Reads text from a character-input stream, buffering characters so as to provide for the efficient reading of characters, arrays, and lines. The buffer size may be specified, or the default size may be used.
**입력 스트림에서 문자를 읽는 함수인데 문자나 배열, 라인들을 효율적으로 읽기 위해서 문자들을 버퍼에 저장하고(버퍼링) 읽는 방법을 취합니다. 버퍼 사이즈는 우리가 지정할 수도 있지만 지정안할 경우에는 기존 디폴트 사이즈가 적용됩니다.**

따라서 알고리즘 풀 때 BufferedReader를 이용한 해설을 많이 볼 수 있습니다. 많은 데이터를 입력받아야 할 상황에서는 BufferedReader를 이용해줍시다.

# BufferedReader 사용방법
BufferedReader의 readLine()을 사용하면 데이터를 라인 단위로 읽을 수 있습니다.
readLine 함수의 리턴 값은 String으로 고정되기 때문에 String이 아닌 다른 타입으로 입력을 받으려면 형변환을 꼭 해줘야 합니다.

~~~java
import java.io.*;

class BufferedReaderEx {
    public static void main(String[] args) {
        try {
            // 예외처리 필수
            // 또는 throwsIOException 하기
            // 콘솔에서 입력 받을 경우
            BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

            // 파일에서 입력받을 경우
            FileReader fr = new FileReader("BufferedReader.txt");
            BufferedReader br_f = new BufferedReader(fr);

            // String이 리턴값이라 형변환 필수, 라인단위
            int num = Integer.parseInt(br.readLine());
            br.close();     // 입출력이 끝난 후 닫아주기.

            // 파일의 한 줄 한 줄 읽어서 출력한다.
            String line = "";

            for (int i = 1; (line = br_f.readLine()) != null; i++) {
                System.out.println(line);
            } 
        } catch (IOException e) {
            e.printStackTrace();
            System.out.println(e.getMessage());
        }
    }
}
~~~

위처럼 한 줄 한 줄 읽습니다. 그런데 공백단위로 데이터를 알려면 어떻게 해야할까요?
Scanner는 공백단위로도 끊어주기 때문에 이런걸 고려할 필요가 없었습니다.

<br>
StringTokenizer의 nextToken함수를 이용하거나 String클래스의 slplit함수를 이용해야 합니다.

<br>
<br>

# BufferedReader클래스의 또 다른 메인 함수들
<br>

Modifier and Type|Method and Description
-|-
void|Close()<br>입력 스트림을 닫고 사용하던 자원들을 풉니다.
void|mark(int, readAheadLimit)<br>스트림의 현재 위치를 마킹합니다.
boolean|markSupported()<br>스트림이 mark 기능을 지원하는지 true/false로 알려줍니다.
int|read()<br>한 글자만 읽어 정수형으로 반환해줍니다.<br>즉 3을 '3'이라고 읽어서 '3'의 정수형인 (int)'3' = 51을 반환<br>3을 친다고 해서 3을 리턴해주는 메소드가 아니다.
int|read(char[] cbuf, int offset, int length)<br>cbuf의 offset 위치부터 length 길이만큼 문자를 스트림으로부터 읽어옵니다.
String|readLine()<br>한줄을 읽는다. String으로 변환
boolean|ready()<br>입력스트림이 사용될 준비가 되어있는지 확인해줌. 1이면 준비완료
void|reset()<br>마킹이 있으면 그 위치부터 다시 시작, 그렇지 않으면 처음부터 다시 시작.
long|skip(long n)<br> n개의 문자를 건나뜀.

<br>
<br>

# BufferedWriter

<br>

마찬가지로 System.out.print(""); 과 동일하게 사용가능한 함수입니다.
BufferedWriter 함수 또한 버퍼를 이용하기 때문에 성능면에서 더 좋다.
많은 양의 출력이 필요할 때에는 마찬가지로 이것을 이용해주는 것이 좋다.

<br>

System.out.println처럼 함수가 문자열 출력과 개행을 동시에 해주지 않기 때문에 개행을 하려면 write에 "\n"를 넣어주거나 newLine함수를 사용해야합니다.

~~~java
import java.io.*;

class BufferedWriterEx {
    public static void main(String[] args) throws IOException {
        BufferedWriter bw = new BufferedWriter(new FileWriter("file.txt"));
        bw.write("hello\n");    // 출력
        bw.nextLine();  // 개행 즉 엔터 역할
        bw.write("Let me write\n");     // 개행과 함께 출력
        bw.flush();     // 남아 있는 데이터를 모두 출력
        bw.close();     // 스트림
    }
}
~~~

버퍼를 이용하는 것이기 때문에 다 쓴 뒤에는 버퍼를 클린하게 해주어야 합니다.
flush() 함수를 통해 버퍼에 남아있는 데이터를 출력해 없앤 후, 스트림을 닫아줍니다.

<br>

# BufferedWriter 클래스의 또 다른 메인 함수들

<br>

Modifier and Type|Method and Description
-|-
void|Close()<br>스트림을 닫습니다. 닫기 전에 flushing 해줍니다.
void|flush()<br>스트림을 비웁니다.
void|newLine()<br>개행역할을 합니다.
void|write(char[] cbuf, int offset, int length)<br>버퍼 offset위치부터 length 크기만큼 write 합니다.
void|write(int c)<br>한 글자 쓰기
void|write(String s, int offset, int length)<br>문자열에서 offset부터 일정 길이만큼 write 해주기.

<br>

# 예제 - 백준 15552번

~~~java
package For.P15552;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;

public class Main {

	public static void main(String[] args) throws NumberFormatException, IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
		int n = Integer.parseInt(br.readLine().trim());
		
		for (int i = 0; i < n; i++) {
			String input = br.readLine();
			String[] inputArray = input.split(" ");
			int a = Integer.parseInt(inputArray[0]);
			int b = Integer.parseInt(inputArray[1]);
			bw.write(a+b + "\n");
		}
		
		bw.flush();
		br.close();
	}

}
~~~

<br>

위 문제의 경우 Scanner로 풀면 정말 간단하지만, 테스트 케이스가 많아진다면 Scanner보단 Buffer을 사용하는것이 효율적이다.

#Printwriter #FileWriter #OutputStreamWriter #StringTokenizer #String

## References
- https://jhnyang.tistory.com/92