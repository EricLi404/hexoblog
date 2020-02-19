---
title: DES算法Java模拟实现
date: 2016-07-10
tags: [Java,code]
description: 本文记录了笔者大二下学期应用密码学课程设计所做内容。
---


> 大二下学期应用密码学课程设计

# 已同步github项目：
url:[https://github.com/EricLi404/Java-Demos/tree/master/Des-Demo](https://github.com/EricLi404/Java-Demos/tree/master/Des-Demo)

git:[https://github.com/EricLi404/Java-Demos.git](https://github.com/EricLi404/Java-Demos.git)


实现了des算法，具体的每一步的输出都有记录，没有加入工作模式，支持加密的输入为英文和数字混合的字符串，填充方式采用的无限填充0的做法。下面是源码。

```

// 主类，含有一些调试信息，各种输出

package cryptology;

import java.util.Scanner;

/**
 * Created by leif on 2016/3/26 0026.
 */
public class des {
	public static void main(String[] args) {
        System.out.println("**********************************************************");
        System.out.println("                      DES加解密分步演示                      ");
        System.out.println("**********************************************************");
        System.out.println();
        @SuppressWarnings("resource")
		Scanner in = new Scanner(System.in);
        System.out.print("请输入明文(支持任意长度字母数字组合)：");
        String mmmmmm = in.nextLine();
        System.out.print("请输入密钥(支持任意格式输入)：");
        String kkkkkk = in.nextLine();
        String[] M = util.all2string(mmmmmm);
        char[] K = util.keypre(kkkkkk);
        descore descore = new descore();
        String mimi = descore.en(M, K);
        String demimi = descore.de(util.all222string(mimi), K);
        String demimistring = util.byte2string(demimi);
        System.out.println("*************************************************");
        System.out.println("**********************结果***********************");
        System.out.println("明文："+mmmmmm);
        System.out.println("------------------------------------------------------------");
        System.out.println("加密结果：");
        System.out.println("二进制："+ mimi);
        System.out.println("十六进制："+util.two2hex(mimi));
        System.out.println("------------------------------------------------------------");
        System.out.println("解密结果：");
        System.out.println("二进制："+demimi);
        System.out.println("十六进制："+util.two2hex(demimi));
        System.out.println("编码后："+demimistring);
            
        
     
		
    }


}
```

```
// 核心算法类
package cryptology;

/**
 * Created by leif on 2016/3/26 0026.
 */
public class descore {

    /**
     * 初始置換
     * @param M char[64]
     * @return char[56]
     */
    private char[] initReplace(char[] M){
        char[] tM = new char[64];
        for (int i = 0; i < 64; i++){
            tM[i] = M[data.IP[i]-1];
        }
        return tM;
    }

    /**
     * 密钥置换 1
     * @param K char[64]
     * @return char[56]
     */
    private char[] keyReplace1(char[] K){
        char[] tK = new char[64];
        for (int i = 0; i < 56; i++){
            tK[i] = K[data.PC_1[i]-1];
        }
        return tK;
    }

    /**
     * 生成子密钥
     * @param K char[56]
     * @param offset 左移位数 int
     * @return char[56]
     */
    private char[] loopKey(char[] K, int offset){
        String k1 = new String();
        String k2 = new String();
        String k = new String(K);
        k1 = (k.substring(0,28)+k.substring(0,2)).substring(offset,offset+28);
        k2 = (k.substring(28,56)+k.substring(28,30)).substring(offset,offset+28);
        return (k1+k2).toCharArray();
    }

    /**
     *  密钥置换2
     * @param K char[56]
     * @return char[48]
     */
    private char[] keyReplace2(char[] K){
        char[] tK = new char[48];
        for (int i = 0; i < 48; i++){
            tK[i] = K[data.PC_2[i]-1];
        }
        return tK;
    }

    /**
     *  生成16轮的轮密钥
     * @param K char[64]
     * @return
     */
    private char[][] getKey(char[] K){
        char[][] tK = new char[16][48];
        char[][] ttK = new char[16][48];
        char[] k1 = new char[56];
        k1 = keyReplace1(K);
        tK[0] = loopKey(k1,data.LeftMove[0]);
        for (int i = 1; i < 16; i++){
            tK[i] = loopKey(tK[i-1],data.LeftMove[i]);
        }
        for(int i = 0; i < 16; i++){
        	ttK[i] = keyReplace2(tK[i]);
        }
        return ttK;
    }

    /**
     * E 表置换（拓展置换）
     * @param R char[32]
     * @return char[48]
     */
    private char[] eReplace(char[] R){
        char[] tR = new char[48];
        for (int i = 0;i < 48; i++){
            tR[i] = R[data.E[i]-1];
        }
        return tR;
    }

    /**
     *  xor异或操作
     * @param tR char[48]
     * @param K char[48] 轮密钥
     * @return
     */
    private char[] xor(char[] tR, char[] K){
        String x = new String();
        for (int i = 0; i < tR.length; i++){
            x += tR[i] ^ K[i];
        }
        return x.toCharArray();
    }


    /**
     * s 盒置换
     * @param xorR char[48]
     * @return char[32]
     */
    private char[] sReplace(char[] xorR){
        char[][] tR = new char[8][6];
/*        String  nn = new String(R);
        System.out.println(nn+nn.length());*/
        String sr = new String();
        for (int i = 0; i < 8; i++){
            for (int j = 0; j < 6; j++){
                tR[i][j] = xorR[i*6+j];
            }
            int p = util.two2ten(String.valueOf(tR[i][0])+String.valueOf(tR[i][5]));
            int q = util.two2ten(String.valueOf(tR[i][1])+String.valueOf(tR[i][2])+String.valueOf(tR[i][3])+String.valueOf(tR[i][4]));
             sr += String.format("%04d",Integer.parseInt(Integer.toBinaryString(data.S_Box[i][p][q])));
//            sr += String.format("%04d",Integer.parseInt(Integer.toBinaryString(data.S_Box[i][(tR[i][0] << 1) + tR[i][5]][(tR[i][1] << 3) + (tR[i][2] << 2) + (tR[i][3] << 1) + tR[i][4]])));
        }
        return sr.toCharArray();
    }

    /**
     * p 表置换
     * @param sR char[32]
     * @return char[32]
     */
    private char[] pReplace(char[] sR){
        char[] pR = new char[32];
        for (int i = 0; i < 32; i++){
            pR[i] = sR[data.P[i]-1];
        }
        return pR;
    }

    /**
     *  终结置换
     * @param R char[64]
     * @return char[64]
     */
    private char[] init_1Replace(char[] R){
        char[] tR = new char[64];
        for (int i = 0; i < 64; i++){
            tR[i] = R[data.IP_1[i]-1];
        }
        return tR;
    }

    /**
     *  轮置换
     * @param R char[32]
     * @param K char[32]
     * @return char[32]
     */
    public char[] core(char[] R, char[] K){
        char[] eR = eReplace(R);
        System.out.println("Etable置换结果---"+new String(eR)+"-----"+util.two2hex(new String(eR)));
        System.out.println("轮密钥---"+new String(K)+"-----"+util.two2hex(new String(K)));
        char[] xorR = xor(eR,K);
        System.out.println("xor-1结果---"+new String(xorR)+"-----"+util.two2hex(new String(xorR)));
        char[] sR = sReplace(xorR);
        System.out.println("Sbox置换结果---"+new String(sR)+"-----"+util.two2hex(new String(sR)));
        char[] pR = pReplace(sR);
        System.out.println("Ptable置换结果---"+new String(pR)+"-----"+util.two2hex(new String(pR)));
        @SuppressWarnings("unused")
		String pp = new String(pR);
        return pR;
    }

    public String enCode(char[] M, char[] K){
    	System.out.println("#########################");
    	System.out.println(" =====  === == ==  = =   开始加密");
    	System.out.println("比特流为---"+new String(M)+"-----"+util.two2hex(new String(M)));
    	System.out.println("密钥为---"+new String(K)+"-----"+util.two2hex(new String(K)));
        char[][] L = new char[17][32];
        char[][] R = new char[17][32];
        char[][] key = new char[16][48];
        char[] res = new char[64];
        char[] r = new char[64];
        M = initReplace(M);
        System.out.println("IP置换结果---"+new String(M)+"-----"+util.two2hex(new String(M)));
        key = getKey(K);
        for (int i = 0; i < 32; i++){
            L[0][i] = M[i];
            R[0][i] = M[i+32];
        }

        for (int i = 1; i < 17; i++){
        	System.out.println("===================第 "+i+" 轮==================");
            char[] xorR = xor(L[i-1],core(R[i-1],key[i-1]));
            System.out.println("xor-2结果"+new String(xorR)+"-----"+util.two2hex(new String(xorR)));
            for (int j = 0; j < 32; j++){
                L[i][j] = R[i-1][j];
                R[i][j] = xorR[j];
            }
        }

        for (int i = 0; i < 32; i++){
            res[i] = R[16][i];
            res[i+32] = L[16][i];
        }
        r = init_1Replace(res);
        String rr = new String(r);
        System.out.println("终结置换结果---"+new String(rr)+"-----"+util.two2hex(new String(rr)));
        return rr;
    }


    public String deCode(char[] M, char[] K){
    	System.out.println("#########################");
    	System.out.println(" =====  === == ==  = =   开始解密");
    	System.out.println("比特流为---"+new String(M)+"-----"+util.two2hex(new String(M)));
    	System.out.println("密钥为---"+new String(K)+"-----"+util.two2hex(new String(K)));
        char[][] L = new char[17][32];
        char[][] R = new char[17][32];
        char[][] key = new char[16][48];
        char[] res = new char[64];
        char[] r = new char[64];
        M = initReplace(M);
        System.out.println("IP置换结果---"+new String(M)+"-----"+util.two2hex(new String(M)));
        key = getKey(K);
        for (int i = 0; i < 32; i++){
            L[0][i] = M[i];
            R[0][i] = M[i+32];
        }
        for (int i = 1; i < 17; i++){
        	System.out.println("===================第 "+i+" 轮==================");
            char[] xorR = xor(L[i-1],core(R[i-1],key[16-i]));
            System.out.println("xor-2结果"+new String(xorR)+"-----"+util.two2hex(new String(xorR)));
            for (int j = 0; j < 32; j++){
                L[i][j] = R[i-1][j];
                R[i][j] = xorR[j];
            }
        }
        for (int i = 0; i < 32; i++){
            res[i] = R[16][i];
            res[i+32] = L[16][i];
        }
        r = init_1Replace(res);
        String rr = new String(r);
        return rr;
    }
    
    public String en(String[] mm, char[] kk) {
    	String mi = "";
		for(int i = 0; i < mm.length; i++){
			char[] charrr = mm[i].toCharArray();
			mi += enCode(charrr, kk);
		}
		return mi;
	}
    
    public String de(String[] mm, char[] kk) {
    	String mi = "";
		for(int i = 0; i < mm.length; i++){
			char[] charrr = mm[i].toCharArray();
			mi += deCode(charrr, kk);
		}
		return mi;
	}
}
```

```
// des算法置换过程需要用到的一些置换表

package cryptology;

/**
 * Created by leif on 2016/3/26 0026.
 */
public class data {
        // 声明常量字节数组
	static int length = 0;
	
    public static int getLength() {
		return length;
	}
	public static void setLength(int length) {
		data.length = length;
	}
	/**
     * 初始置换
     */
    static final int[] IP = {
                58, 50, 42, 34, 26, 18, 10, 2, 60, 52, 44, 36, 28, 20, 12, 4, 62, 54,
                46, 38, 30, 22, 14, 6, 64, 56, 48, 40, 32, 24, 16, 8, 57, 49, 41, 33,
                25, 17, 9, 1, 59, 51, 43, 35, 27, 19, 11, 3, 61, 53, 45, 37, 29, 21,
                13, 5, 63, 55, 47, 39, 31, 23, 15, 7
        }; // 64

    /**
     *
     */
    static final int[] IP_1 = {
                40, 8, 48, 16, 56, 24, 64, 32, 39, 7, 47, 15, 55, 23, 63, 31, 38, 6,
                46, 14, 54, 22, 62, 30, 37, 5, 45, 13, 53, 21, 61, 29, 36, 4, 44, 12,
                52, 20, 60, 28, 35, 3, 43, 11, 51, 19, 59, 27, 34, 2, 42, 10, 50, 18,
                58, 26, 33, 1, 41, 9, 49, 17, 57, 25
        }; // 64


    /**
     * 密钥置换  1
     */
    static final int[] PC_1 = {
                57, 49, 41, 33, 25, 17, 9, 1, 58, 50, 42, 34, 26, 18, 10, 2, 59, 51,
                43, 35, 27, 19, 11, 3, 60, 52, 44, 36, 63, 55, 47, 39, 31, 23, 15, 7,
                62, 54, 46, 38, 30, 22, 14, 6, 61, 53, 45, 37, 29, 21, 13, 5, 28, 20,
                12, 4
        }; // 56

    /**
     *  密钥置换  2
     */
    static final int[] PC_2 = {
                14, 17, 11, 24, 1, 5, 3, 28, 15, 6, 21, 10, 23, 19, 12, 4, 26, 8, 16,
                7, 27, 20, 13, 2, 41, 52, 31, 37, 47, 55, 30, 40, 51, 45, 33, 48, 44,
                49, 39, 56, 34, 53, 46, 42, 50, 36, 29, 32
        }; // 48

    /**
     *  E表置换（将密钥的右32置换为48）
     */
    static final int[] E = {
                32, 1, 2, 3, 4, 5, 4, 5, 6, 7, 8, 9, 8, 9, 10, 11, 12, 13, 12, 13,
                14, 15, 16, 17, 16, 17, 18, 19, 20, 21, 20, 21, 22, 23, 24, 25, 24,
                25, 26, 27, 28, 29, 28, 29, 30, 31, 32, 1
        }; // 48

        static final int[] P = {
                16, 7, 20, 21, 29, 12, 28, 17, 1, 15, 23, 26, 5, 18, 31, 10, 2, 8,
                24, 14, 32, 27, 3, 9, 19, 13, 30, 6, 22, 11, 4, 25
        }; // 32
        static final int[][][] S_Box = {
                {
                        { 14, 4, 13, 1, 2, 15, 11, 8, 3, 10, 6, 12, 5, 9, 0, 7 },
                        { 0, 15, 7, 4, 14, 2, 13, 1, 10, 6, 12, 11, 9, 5, 3, 8 },
                        { 4, 1, 14, 8, 13, 6, 2, 11, 15, 12, 9, 7, 3, 10, 5, 0 },
                        { 15, 12, 8, 2, 4, 9, 1, 7, 5, 11, 3, 14, 10, 0, 6, 13 }
                },
                { // S_Box[1]
                        { 15, 1, 8, 14, 6, 11, 3, 4, 9, 7, 2, 13, 12, 0, 5, 10 },
                        { 3, 13, 4, 7, 15, 2, 8, 14, 12, 0, 1, 10, 6, 9, 11, 5 },
                        { 0, 14, 7, 11, 10, 4, 13, 1, 5, 8, 12, 6, 9, 3, 2, 15 },
                        { 13, 8, 10, 1, 3, 15, 4, 2, 11, 6, 7, 12, 0, 5, 14, 9 }
                },
                { // S_Box[2]
                        { 10, 0, 9, 14, 6, 3, 15, 5, 1, 13, 12, 7, 11, 4, 2, 8 },
                        { 13, 7, 0, 9, 3, 4, 6, 10, 2, 8, 5, 14, 12, 11, 15, 1 },
                        { 13, 6, 4, 9, 8, 15, 3, 0, 11, 1, 2, 12, 5, 10, 14, 7 },
                        { 1, 10, 13, 0, 6, 9, 8, 7, 4, 15, 14, 3, 11, 5, 2, 12 }
                },
                { // S_Box[3]
                        { 7, 13, 14, 3, 0, 6, 9, 10, 1, 2, 8, 5, 11, 12, 4, 15 },
                        { 13, 8, 11, 5, 6, 15, 0, 3, 4, 7, 2, 12, 1, 10, 14, 9 },
                        { 10, 6, 9, 0, 12, 11, 7, 13, 15, 1, 3, 14, 5, 2, 8, 4 },
                        { 3, 15, 0, 6, 10, 1, 13, 8, 9, 4, 5, 11, 12, 7, 2, 14 }
                },
                { // S_Box[4]
                        { 2, 12, 4, 1, 7, 10, 11, 6, 8, 5, 3, 15, 13, 0, 14, 9 },
                        { 14, 11, 2, 12, 4, 7, 13, 1, 5, 0, 15, 10, 3, 9, 8, 6 },
                        { 4, 2, 1, 11, 10, 13, 7, 8, 15, 9, 12, 5, 6, 3, 0, 14 },
                        { 11, 8, 12, 7, 1, 14, 2, 13, 6, 15, 0, 9, 10, 4, 5, 3 }
                },
                { // S_Box[5]
                        { 12, 1, 10, 15, 9, 2, 6, 8, 0, 13, 3, 4, 14, 7, 5, 11 },
                        { 10, 15, 4, 2, 7, 12, 9, 5, 6, 1, 13, 14, 0, 11, 3, 8 },
                        { 9, 14, 15, 5, 2, 8, 12, 3, 7, 0, 4, 10, 1, 13, 11, 6 },
                        { 4, 3, 2, 12, 9, 5, 15, 10, 11, 14, 1, 7, 6, 0, 8, 13 }
                },
                { // S_Box[6]
                        { 4, 11, 2, 14, 15, 0, 8, 13, 3, 12, 9, 7, 5, 10, 6, 1 },
                        { 13, 0, 11, 7, 4, 9, 1, 10, 14, 3, 5, 12, 2, 15, 8, 6 },
                        { 1, 4, 11, 13, 12, 3, 7, 14, 10, 15, 6, 8, 0, 5, 9, 2 },
                        { 6, 11, 13, 8, 1, 4, 10, 7, 9, 5, 0, 15, 14, 2, 3, 12 }
                },
                { // S_Box[7]
                        { 13, 2, 8, 4, 6, 15, 11, 1, 10, 9, 3, 14, 5, 0, 12, 7 },
                        { 1, 15, 13, 8, 10, 3, 7, 4, 12, 5, 6, 11, 0, 14, 9, 2 },
                        { 7, 11, 4, 1, 9, 12, 14, 2, 0, 6, 10, 13, 15, 3, 5, 8 },
                        { 2, 1, 14, 7, 4, 10, 8, 13, 15, 12, 9, 0, 3, 5, 6, 11 }
                } // S_Box[8]
        };
        static final int[] LeftMove = {
                1, 1, 2, 2, 2, 2, 2, 2, 1, 2, 2, 2, 2, 2, 2, 1
        }; // 左移位置列表
}
```

```
// 系统里用到的一些公共的功能性函数

package cryptology;

import java.util.HashMap;

/**
 * Created by leif on 2016/6/21.
 */
public class util {
    /**
     *  二进制转十进制
     * @param two 二进制 string
     * @return 十进制 int
     */
    public static int two2ten(String two){
        String s = two;
        int x = 0;
        int pow = 0;
        for (int i = s.length() - 1; i >= 0; i--) {
            x += Math.pow(2, pow) * (s.charAt(i) == '1' ? 1 : 0);
            pow ++;
        }
        return x;
    }
    
    
    /**
     *  二进制转十六进制
     * @param two 二进制 string
     * @return 十六进制 int
     */
    public static String two2hex(String bString){
    	if (bString == null || bString.equals("") || bString.length() % 8 != 0)  
            return null;  
        StringBuffer tmp = new StringBuffer();  
        int iTmp = 0;  
        for (int i = 0; i < bString.length(); i += 4)  
        {  
            iTmp = 0;  
            for (int j = 0; j < 4; j++)  
            {  
                iTmp += Integer.parseInt(bString.substring(i + j, i + j + 1)) << (4 - j - 1);  
            }  
            tmp.append(Integer.toHexString(iTmp));  
        }  
        return tmp.toString();  
    }  
    

    /**
     *  将64位int数组转化为64位char数组
     * @param mm int[64]
     * @return char[64]
     */
    public static char[] int64tochar64(int[] mm){
        @SuppressWarnings("unused")
		char[] g = new char[64];
        String gg = new String();
        for (int i = 0; i < 64; i++){
            gg += mm[i];
        }
        return gg.toCharArray();
    }

    public static HashMap<Integer,char[]> preMI(String mmmmmm){
    	String lmmmmmm = "";
    	for(int i = 0; i < mmmmmm.length(); i++){
    		lmmmmmm += Integer.toBinaryString(Integer.parseInt(mmmmmm.substring(i,i+1)));
    	}
    	HashMap<Integer, char[] > tmmm = new HashMap<>();
    	char mmmmm[] = lmmmmmm.toCharArray();
    	char rm[] = new char[64];
    	int j = 0;
    	for(int i = 0; i < lmmmmmm.length(); i++){
    		rm[i%64] = mmmmm[i];
    		if (i % 64 == 63) {
				tmmm.put(j, rm);
				j++;
			}
    	}
    	return tmmm;
    }
    
    
    /**
     * 明文初始化处理，将每一位转为一个64bit长度的string
     * @param all
     * @return
     */
    public static String[] all2string(String all)  {
		byte byteall[] = all.getBytes();
		String allstring[] = new String[all.length()];
		for(int i = 0; i < byteall.length; i++){
			allstring[i] =   String.format("%064d", Integer.parseInt(Integer.toBinaryString(byteall[i])));
		}
		return allstring;
	}
    
    /**
     * 密文解密预处理，将密文每64bit截取成一个string
     * @param all
     * @return
     */
    public static String[] all222string(String all)  {
		String allstring[] = new String[all.length()/64];
		for(int i = 0; i < allstring.length; i++){
			allstring[i] =   all.substring(i*64,(i+1)*64);
		}
		return allstring;
	}
    
    /**
     * 将解密结果的byte转为string，还原密文
     * @param all
     * @return
     */
    public static String byte2string(String all)  {
		byte allstring[] = new byte[all.length()/64];
		for(int i = 0; i < allstring.length; i++){
			allstring[i] =   (byte) two2ten(all.substring(i*64,(i+1)*64).substring(56, 64));
		}
		return new String(allstring);
	}
    
    /**
     * 将任意格式的密钥获取hash值，格式化为char[64]
     * @param k
     * @return
     */
    public static char[] keypre(String k) {
		String ha = String.format("%08d", k.hashCode());
		String kk = "";
		for(int i = 0; i < 8; i++){
			kk += String.format("%08d", Integer.parseInt(Integer.toBinaryString(Integer.parseInt(ha.substring(i, i+1)))));
		}
		return kk.toCharArray();
	}
}
```