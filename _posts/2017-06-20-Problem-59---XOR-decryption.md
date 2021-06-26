---
layout: post
title: "Problem 59 - XOR decryption"
date: 2017-06-20 09:46
mathjax: true
number: 59
---

## Question

Each character on a computer is assigned a unique code and the  preferred standard is ASCII (American Standard Code for Information Interchange). For example, uppercase A = 65, asterisk (*) = 42, and  lowercase k = 107.

A modern encryption method is to take a text file, convert the bytes to ASCII, then XOR each byte with a given value, taken from a secret key. The advantage with the XOR function is that using the same encryption key on the cipher text, restores the plain text; for example, 65 XOR 42 = 107, then 107 XOR 42 = 65.

For unbreakable encryption, the key is the same length as the plain text message, and the key is made up of random bytes. The user would keep the encrypted message and the encryption key in different locations, and without both "halves", it is impossible to decrypt the message.

Unfortunately, this method is impractical for most users, so the modified method is to use a password as a key. If the password is shorter than the message, which is likely, the key is repeated cyclically throughout the message. The balance for this method is using a sufficiently long password key for security, but short enough to be memorable.

Your task has been made easy, as the encryption key consists of three lower case characters. Using [p059_cipher.txt](https://projecteuler.net/project/resources/p059_cipher.txt)  (right click and 'Save Link/Target As...'), a file containing the encrypted ASCII codes, and the knowledge that the plain text must contain common English words, decrypt the message and find the sum of the ASCII values in the original text.

## Answer

Something different this time in the realm of cryptography. It seems pretty straightforward: Try every combination of three lowercase letters, decrypt the message, and see if it makes sense. However, there's a couple of problems here:

- All combinations of three lowercase letters is $$26^3 = 17\,576$$, which might not be too bad in this problem, but can get unruly if we have any more letters.
- What on earth does "if it makes sense" mean? Sure, it means "the excerpt is read properly and is legible", but how do we translate that into logic?

For the first bullet point, we can take this approach: Since the sequence of three letters will be repeated, that means each letter in the key will decrypt every **third** letter in the encrypted message. We can see if every third letter is indeed a letter, digit, or special character. As an example, say the first letter of a test key is 'b'. If when decrypting every third letter in the encrypted message (starting from the first one) results in strange characters that can't possibly be part of regular words, then we know all keys starting with 'b' are invalid! In this way, we reduce the number of possible characters in each position of the key to something much less than $$26^3$$.

For the second point, note that the problem says that the text contains common English words. However, another way to say this, is that "letters occur where they are supposed to", or more quantitatively, **the distribution of the letters in the decrypted text follow closely with that of the English language**. That is something we can compare using code! Now, the distribution of the letters in the English language can be seen at [this Wikipedia page](https://en.wikipedia.org/wiki/Letter_frequency). To measure "closeness", we can put these values into a vector, and calculate the distance to the letter distribution vector of a decrypted message. The key which produces the smallest distance to the ground truth distribution will be the correct key!

So let's put this in code. Python provides built-in functions `chr()` and `ord()` which allow us to go back and forth between the character and the integer it represents. To perform an XOR operation between two integers, we use the `^` operator ("Oh so that's what the carat is used for!" :)). In that first for loop, I check to see the possible letters in each position of the key. Any character with an ASCII value less than 32 (32 is space) and greater than 122 is invalid with respect to this problem. I save all possible letters at each position into a double array.

In order to go through combinations then, I use the `product` function from the `itertools` which gives quick looping. Notice I save the ground truth distribution array as well. Using each possible key, I decrypt the message, filter out anything which aren't letters, calculate the frequency distribution, and then use `np.linalg.norm` to figure out the distance to the ground truth distribution. The least distance and associated key is then saved, which I then use to calculate the sum of all decrypted integers, which is what the problem is asking for. Note the use of the `string` package to grab the lowercase letters.

```python
with open('p059_cipher.txt') as f:
    encryptedMess = [int(a) for a in f.readline().split(',')]

# Find the only possible letters that could
# be part of the enccryption key.
encryptedPosses = []
for i in range(3):
    possLetters = []
    for letter in string.ascii_lowercase:
        intRep = ord(letter)
        # Encrypt every 3rd letter starting
        # from i...
        decryptedCharas = [intRep ^ encInt for encInt in encryptedMess[i::3]]
        # All of the decrypted charas have to be
        # letters, digits, special charas, or spaces.
        # So integer is between 32 and 122.
        if all(32 <= charaInt <= 122 for charaInt in decryptedCharas):
            possLetters.append(letter)
    encryptedPosses.append(possLetters)

# Ground truth frequency for each letter
# in english language (from Wikipedia)
groundTruth = [0.08167, 0.01492, 0.02782, 0.04253, 0.12702, 0.02228, 0.02015,
               0.06094, 0.06966, 0.00153, 0.00772, 0.04025, 0.02406, 0.06749,
               0.07507, 0.01929, 0.00095, 0.05987, 0.06327, 0.09056, 0.02758,
               0.00978, 0.02360, 0.00150, 0.01974, 0.00074]
bestKey = ''
closestDist = float('inf')
for possibleKey in product(*encryptedPosses):
    # Decrypt text given this key
    decryptedText = [chr(ord(possibleKey[i % 3]) ^ encryptedMess[i]) for i in range(len(encryptedMess))]
    # Turn everything to lowercase and grab
    # only letters...
    onlyLetters = [chara.lower() for chara in decryptedText if chara.lower() in string.ascii_lowercase]
    # Calculate counts of each letter
    letterCounts = np.zeros(26)
    for letter in onlyLetters:
        letterCounts[ord(letter) - 97] += 1
    # Calculate frequency
    letterFreq = letterCounts / np.sum(letterCounts)
    # Calculate distance to ground truth
    # distribution
    distance = np.linalg.norm(letterFreq - groundTruth)
    if distance < closestDist:
        closestDist = distance
        bestKey = ''.join(possibleKey)

print('Best key is "{}".'.format(bestKey))

# Now decrypt message and add all
# integers...
decryptedSum = sum([ord(bestKey[i % 3]) ^ encryptedMess[i] for i in range(len(encryptedMess))])
print('Sum of decrypted integers: {}'.format(decryptedSum))
```

Running the entire code above results in an output of,

```
Best key is "god".
Sum of decrypted integers: 107359
0.07973290677218499 seconds.
```

Therefore, the key to the message is "god" and the sum is **107359**. What's the decrypted message? I'll leave it to you :)

### Update July 23, 2019

So after a small revisit to this problem, I discovered that due to the religious nature of the key and decrypted text, the encrypted message has been changed as of February 5, 2019. You will notice that the cipher link above doesn't produce the respective output above. Instead, it produces,

```
Best key is "exp".
Sum of decrypted integers: 129448
0.02513697772366312 seconds.
```

Note that there is no change needed in the original program, as the problem itself didn't change, only the input.