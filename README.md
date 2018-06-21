KeygenMe "Psychic powers or brute strength your choice" ([task source](https://forum.tuts4you.com/topic/37904-keygenme-01-psychic-powers-or-brute-strength-your-choice)) **brute-force solution**

### Program flow explanation

program accept:
	```5 <= len(NAME) < 256
	16 <= len(KEY) < 256```
it lowercase all `NAME` chars, and  delete from name all non `a`-`z` chars. Then it delete all non base64-alphabet chars from KEY, and check if `len(KEY) == 16`
Lets consider `NAME` as string with length 5 or greater, and which consist from 'a'-'z' chars.
Lets consider `KEY` as string with length 16, and which consist from base64-alphabet chars (`+,/',0-9,A-Z,a-z`).

Then program compare if NAME:KEY pair is not 
+ "lostit":"_RY5obY7IduF4Se2T_"
+ "tutsyou":"_RkIomczPYHNrJoCA_"

which are valid NAME:KEY pairs, but for some reason blacklisted in such way.

Now your program begin to calculate different hashes and make different CHECKS:

**FIRST HASH**: hash retrieved from `KEY`, lets give him a name `K_hash_1`. `K_hash_1` is 12 byte hash, each 4 bytes of `KEY` converted to 3 bytes of `K_hash_1` . Below code snippet explain this transformation, consider `get_K_hash_1()` function:
```C
char base64alphabet_map[] = {
	/* 01*/ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
	/* 10*/ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
	/* 20*/ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
	/* 30*/ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
	/* 40*/ 0, 0, 0,62, 0, 0, 0,63,51,53,/*43:+,47:/,48:0*/
	/* 50*/54,55,56,57,58,59,60,61, 0, 0,
	/* 60*/ 0, 0, 0, 0, 0, 0, 1, 2, 3, 4,/*65:A*/
	/* 70*/ 5, 6, 7, 8, 9,10,11,12,13,14,
	/* 80*/15,16,17,18,19,20,21,22,23,24,
	/* 90*/25, 0, 0, 0, 0, 0, 0,26,27,28,/*90:Z,97:a*/
	/*100*/29,30,31,32,33,34,35,36,37,38,
	/*110*/39,40,41,42,43,44,45,46,47,48,
	/*120*/49,50,51, 0, 0, 0, 0, 0, 0, 0/*122:z*/
};


void get_K_hash_1_quarter(const char in[4], char out[3]) {
	unsigned int c0 = base64alphabet_map[in[0]];
	unsigned int c1 = base64alphabet_map[in[1]];
	unsigned int c2 = base64alphabet_map[in[2]];
	unsigned int c3 = base64alphabet_map[in[3]];

	unsigned int hash_chunk = (((((c0 << 6) + c1) << 6) + c2) << 6) + c3;

	unsigned int temp_hash;
	temp_hash = hash_chunk >> 16;
	out[0] = (char)temp_hash;
	temp_hash = hash_chunk >> 8;
	out[1] = (char)temp_hash;
	out[2] = (char)hash_chunk;
}

void get_K_hash_1(const char *key, char custom_hash[16]) {
	for (int i = 0, n = 0; i < KEY_LEN, n < HASH_LEN; i = i + 4, n = n + 3) {
		get_K_hash_1_quarter(key + i, custom_hash + n);
	}
}
```
