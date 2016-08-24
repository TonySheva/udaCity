postgresql 基本操作。

create table ，dump table data 可以在world db 查询

INSERT INTO user (name, web_site) VALUES ('tony', 'github.com/TonySheva')

DELETE FROM user where id=1;

UPDATE user SET name='sheva' where id=1;

INSERT INTO image_hash_link_logs(image_creative_hash, 
			image_facebook_hash)
			SELECT DISTINCT img.hash, log.name 
			from image_creatives as img
			inner join facebook_ad_image_logs as log 
			on substring(RIGHT(log.param_text, 38) from -1 for 38)=img.hash 

postgresql 一些有趣的函数 

Table 9-6. SQL String Functions and Operators

Function	Return Type	Description	Example	Result
string || string	text	String concatenation	'Post' || 'greSQL'	PostgreSQL
string || non-string or non-string || string	text	String concatenation with one non-string input	'Value: ' || 42	Value: 42
bit_length(string)	int	Number of bits in string	bit_length('jose')	32
char_length(string) or character_length(string)	int	Number of characters in string	char_length('jose')	4
lower(string)	text	Convert string to lower case	lower('TOM')	tom
octet_length(string)	int	Number of bytes in string	octet_length('jose')	4
overlay(string placing string from int [for int])	text	Replace substring	overlay('Txxxxas' placing 'hom' from 2 for 4)	Thomas
position(substring in string)	int	Location of specified substring	position('om' in 'Thomas')	3
substring(string [from int] [for int])	text	Extract substring	substring('Thomas' from 2 for 3)	hom
substring(string from pattern)	text	Extract substring matching POSIX regular expression. See Section 9.7 for more information on pattern matching.	substring('Thomas' from '...$')	mas
substring(string from pattern for escape)	text	Extract substring matching SQL regular expression. See Section 9.7 for more information on pattern matching.	substring('Thomas' from '%#"o_a#"_' for '#')	oma
trim([leading | trailing | both] [characters] from string)	text	Remove the longest string containing only the characters (a space by default) from the start/end/both ends of the string	trim(both 'x' from 'xTomxx')	Tom
upper(string)	text	Convert string to upper case	upper('tom')	TOM
Additional string manipulation functions are available and are listed in Table 9-7. Some of them are used internally to implement the SQL-standard string functions listed in Table 9-6.

Table 9-7. Other String Functions

Function	Return Type	Description	Example	Result
ascii(string)	int	ASCII code of the first character of the argument. For UTF8 returns the Unicode code point of the character. For other multibyte encodings, the argument must be an ASCII character.	ascii('x')	120
btrim(string text [, characters text])	text	Remove the longest string consisting only of characters in characters (a space by default) from the start and end of string	btrim('xyxtrimyyx', 'xy')	trim
chr(int)	text	Character with the given code. For UTF8 the argument is treated as a Unicode code point. For other multibyte encodings the argument must designate an ASCII character. The NULL (0) character is not allowed because text data types cannot store such bytes.	chr(65)	A
concat(str "any" [, str "any" [, ...] ])	text	Concatenate all arguments. NULL arguments are ignored.	concat('abcde', 2, NULL, 22)	abcde222
concat_ws(sep text, str "any" [, str "any" [, ...] ])	text	Concatenate all but first arguments with separators. The first parameter is used as a separator. NULL arguments are ignored.	concat_ws(',', 'abcde', 2, NULL, 22)	abcde,2,22
convert(string bytea, src_encoding name, dest_encoding name)	bytea	Convert string to dest_encoding. The original encoding is specified by src_encoding. The string must be valid in this encoding. Conversions can be defined by CREATE CONVERSION. Also there are some predefined conversions. See Table 9-8 for available conversions.	convert('text_in_utf8', 'UTF8', 'LATIN1')	text_in_utf8 represented in Latin-1 encoding (ISO 8859-1)
convert_from(string bytea, src_encoding name)	text	Convert string to the database encoding. The original encoding is specified by src_encoding. The string must be valid in this encoding.	convert_from('text_in_utf8', 'UTF8')	text_in_utf8 represented in the current database encoding
convert_to(string text, dest_encoding name)	bytea	Convert string to dest_encoding.	convert_to('some text', 'UTF8')	some text represented in the UTF8 encoding
decode(string text, format text)	bytea	Decode binary data from textual representation in string. Options for format are same as in encode.	decode('MTIzAAE=', 'base64')	\x3132330001
encode(data bytea, format text)	text	Encode binary data into a textual representation. Supported formats are: base64, hex, escape. escape converts zero bytes and high-bit-set bytes to octal sequences (\nnn) and doubles backslashes.	encode(E'123\\000\\001', 'base64')	MTIzAAE=
format(formatstr text [, str "any" [, ...] ])	text	Format a string. This function is similar to the C function sprintf; but only the following conversion specifications are recognized: %s interpolates the corresponding argument as a string; %I escapes its argument as an SQL identifier; %L escapes its argument as an SQL literal; %% outputs a literal %. A conversion can reference an explicit parameter position by preceding the conversion specifier with n$, where n is the argument position. See also Example 39-1.	format('Hello %s, %1$s', 'World')	Hello World, World
initcap(string)	text	Convert the first letter of each word to upper case and the rest to lower case. Words are sequences of alphanumeric characters separated by non-alphanumeric characters.	initcap('hi THOMAS')	Hi Thomas
left(str text, n int)	text	Return first n characters in the string. When n is negative, return all but last |n| characters.	left('abcde', 2)	ab
length(string)	int	Number of characters in string	length('jose')	4
length(string bytea, encoding name )	int	Number of characters in string in the given encoding. The string must be valid in this encoding.	length('jose', 'UTF8')	4
lpad(string text, length int [, fill text])	text	Fill up the string to length length by prepending the characters fill (a space by default). If the string is already longer than length then it is truncated (on the right).	lpad('hi', 5, 'xy')	xyxhi
ltrim(string text [, characters text])	text	Remove the longest string containing only characters from characters (a space by default) from the start of string	ltrim('zzzytrim', 'xyz')	trim
md5(string)	text	Calculates the MD5 hash of string, returning the result in hexadecimal	md5('abc')	900150983cd24fb0 d6963f7d28e17f72
pg_client_encoding()	name	Current client encoding name	pg_client_encoding()	SQL_ASCII
quote_ident(string text)	text	Return the given string suitably quoted to be used as an identifier in an SQL statement string. Quotes are added only if necessary (i.e., if the string contains non-identifier characters or would be case-folded). Embedded quotes are properly doubled. See also Example 39-1.	quote_ident('Foo bar')	"Foo bar"
quote_literal(string text)	text	Return the given string suitably quoted to be used as a string literal in an SQL statement string. Embedded single-quotes and backslashes are properly doubled. Note that quote_literal returns null on null input; if the argument might be null, quote_nullable is often more suitable. See also Example 39-1.	quote_literal(E'O\'Reilly')	'O''Reilly'
quote_literal(value anyelement)	text	Coerce the given value to text and then quote it as a literal. Embedded single-quotes and backslashes are properly doubled.	quote_literal(42.5)	'42.5'
quote_nullable(string text)	text	Return the given string suitably quoted to be used as a string literal in an SQL statement string; or, if the argument is null, return NULL. Embedded single-quotes and backslashes are properly doubled. See also Example 39-1.	quote_nullable(NULL)	NULL
quote_nullable(value anyelement)	text	Coerce the given value to text and then quote it as a literal; or, if the argument is null, return NULL. Embedded single-quotes and backslashes are properly doubled.	quote_nullable(42.5)	'42.5'
regexp_matches(string text, pattern text [, flags text])	setof text[]	Return all captured substrings resulting from matching a POSIX regular expression against the string. See Section 9.7.3 for more information.	regexp_matches('foobarbequebaz', '(bar)(beque)')	{bar,beque}
regexp_replace(string text, pattern text, replacement text [, flags text])	text	Replace substring(s) matching a POSIX regular expression. See Section 9.7.3 for more information.	regexp_replace('Thomas', '.[mN]a.', 'M')	ThM
regexp_split_to_array(string text, pattern text [, flags text ])	text[]	Split string using a POSIX regular expression as the delimiter. See Section 9.7.3 for more information.	regexp_split_to_array('hello world', E'\\s+')	{hello,world}
regexp_split_to_table(string text, pattern text [, flags text])	setof text	Split string using a POSIX regular expression as the delimiter. See Section 9.7.3 for more information.	regexp_split_to_table('hello world', E'\\s+')	hello
world

(2 rows)
repeat(string text, number int)	text	Repeat string the specified number of times	repeat('Pg', 4)	PgPgPgPg
replace(string text, from text, to text)	text	Replace all occurrences in string of substring from with substring to	replace('abcdefabcdef', 'cd', 'XX')	abXXefabXXef
reverse(str)	text	Return reversed string.	reverse('abcde')	edcba
right(str text, n int)	text	Return last n characters in the string. When n is negative, return all but first |n| characters.	right('abcde', 2)	de
rpad(string text, length int [, fill text])	text	Fill up the string to length length by appending the characters fill (a space by default). If the string is already longer than length then it is truncated.	rpad('hi', 5, 'xy')	hixyx
rtrim(string text [, characters text])	text	Remove the longest string containing only characters from characters (a space by default) from the end of string	rtrim('trimxxxx', 'x')	trim
split_part(string text, delimiter text, field int)	text	Split string on delimiter and return the given field (counting from one)	split_part('abc~@~def~@~ghi', '~@~', 2)	def
strpos(string, substring)	int	Location of specified substring (same as position(substring in string), but note the reversed argument order)	strpos('high', 'ig')	2
substr(string, from [, count])	text	Extract substring (same as substring(string from from for count))	substr('alphabet', 3, 2)	ph
to_ascii(string text [, encoding text])	text	Convert string to ASCII from another encoding (only supports conversion from LATIN1, LATIN2, LATIN9, and WIN1250 encodings)	to_ascii('Karel')	Karel
to_hex(number int or bigint)	text	Convert number to its equivalent hexadecimal representation	to_hex(2147483647)	7fffffff
translate(string text, from text, to text)	text	Any character in string that matches a character in the from set is replaced by the corresponding character in the to set. If from is longer than to, occurrences of the extra characters in from are removed.	translate('12345', '143', 'ax')	a2x5
