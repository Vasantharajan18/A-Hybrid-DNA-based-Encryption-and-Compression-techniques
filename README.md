# A-Hybrid-DNA-based-Encryption-and-Compression-techniques
Hybrid encryption system combining Caesar cipher, DNA encoding, LZW compression, and visual cryptography to ensure secure data transmission. This layered approach transforms plaintext into cipher text, encodes it into DNA strands, compresses it efficiently, and converts it into visual shares for high-level data protection and privacy

// Project: Hybrid DNA-Based Image Encryption and Decryption
// Language: Java

import java.util.*;
import java.awt.image.BufferedImage;
import javax.imageio.ImageIO;
import java.io.*;

public class HybridDNAEncryption {

    // Step 1: Caesar Cipher Encryption
    public static String caesarCipherEncrypt(String text, int key) {
        StringBuilder result = new StringBuilder();
        key = key % 26;
        for (char c : text.toCharArray()) {
            if (Character.isUpperCase(c)) {
                result.append((char) ('A' + (c - 'A' + key) % 26));
            } else if (Character.isLowerCase(c)) {
                result.append((char) ('a' + (c - 'a' + key) % 26));
            } else {
                result.append(c);
            }
        }
        return result.toString();
    }

    // Step 2: Convert to Binary
    public static String toBinary(String text) {
        StringBuilder binary = new StringBuilder();
        for (char c : text.toCharArray()) {
            binary.append(String.format("%8s", Integer.toBinaryString(c)).replace(' ', '0'));
        }
        return binary.toString();
    }

    // Step 3: Binary to DNA Encoding
    public static String binaryToDNA(String binary) {
        StringBuilder dna = new StringBuilder();
        for (int i = 0; i < binary.length(); i += 2) {
            String pair = binary.substring(i, i + 2);
            switch (pair) {
                case "00": dna.append("A"); break;
                case "01": dna.append("C"); break;
                case "10": dna.append("G"); break;
                case "11": dna.append("T"); break;
            }
        }
        return dna.toString();
    }

    // Step 4: LZW Compression
    public static List<Integer> lzwCompress(String input) {
        Map<String, Integer> dictionary = new HashMap<>();
        for (int i = 0; i < 256; i++) {
            dictionary.put("" + (char) i, i);
        }
        String w = "";
        List<Integer> result = new ArrayList<>();
        int dictSize = 256;

        for (char c : input.toCharArray()) {
            String wc = w + c;
            if (dictionary.containsKey(wc)) {
                w = wc;
            } else {
                result.add(dictionary.get(w));
                dictionary.put(wc, dictSize++);
                w = "" + c;
            }
        }

        if (!w.equals("")) {
            result.add(dictionary.get(w));
        }

        return result;
    }

    // Step 5: Visual Cryptography - hide data in image (Simple LSB technique)
    public static void hideDataInImage(List<Integer> data, String outputImagePath) throws IOException {
        BufferedImage image = new BufferedImage(256, 256, BufferedImage.TYPE_INT_RGB);
        int x = 0, y = 0;

        for (int val : data) {
            int rgb = (val << 16) | (val << 8) | val;
            image.setRGB(x, y, rgb);
            x++;
            if (x >= 256) {
                x = 0;
                y++;
            }
        }

        ImageIO.write(image, "png", new File(outputImagePath));
    }

    public static void main(String[] args) throws IOException {
        String input = "VIT";
        int key = 15;

        // Step 1
        String cipher = caesarCipherEncrypt(input, key);
        System.out.println("Caesar Cipher: " + cipher);

        // Step 2
        String binary = toBinary(cipher);
        System.out.println("Binary: " + binary);

        // Step 3
        String dna = binaryToDNA(binary);
        System.out.println("DNA: " + dna);

        // Step 4
        List<Integer> compressed = lzwCompress(dna);
        System.out.println("LZW Compressed: " + compressed);

        // Step 5
        hideDataInImage(compressed, "encrypted_output.png");
        System.out.println("Image created with encrypted data.");
    }
}
