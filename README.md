# Limited-Turkish-Programming-Language-Compiler
The code checks syntax of given calc.in file including 3 parts that are AnaDegiskenler, YeniDegiskenler and Sonuc according to specified rules.

```
import re
import sys
parts = ['AnaDegiskenler', 'YeniDegiskenler', 'Sonuc']
t_digit = ['sifir', 'bir', 'iki', 'uc', 'dort', 'bes', 'alti', 'yedi', 'sekiz', 'dokuz']
l_term = ['dogru', 'yanlis']
digit = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9']
digit_digit = []
for a in range(10):
    for b in range(10):
        digit_digit.append(str(a+b/10))  # like ['0.0', '0.1' ...]
t_digit_t_digit = []
for a in t_digit:
    for b in t_digit:
        t_digit_t_digit.append(a+' nokta '+b)   # like ['sifir nokta sifir', 'sifir nokta bir' ...]
a_term = digit + t_digit + digit_digit + t_digit_t_digit
binaop = ['+', '-', '*', 'arti', 'eksi', 'carpi']
binlop = ['ve', 'veya']
keywords = digit + t_digit + l_term + parts + binaop + binlop + ['degeri', 'olsun', 'nokta', 'ac-parantez', 'kapa-parantez', '(', ')']
dict_of_var_name = {}


def error():
    output = open('calc.out', 'w')
    output.write("Dont Let Me Down")
    output.close()

# exp_ctrl doing syntax check in the string (s) not including parathesis
# returns a shorter string representing the same type(either arithmetic or logic) as input string to be used instead of s later.
# If False,that will be used for error() later.


def exp_ctrl(s):
    if (' ve ' in s) or (' veya ' in s):
        # If there are binop in string, there shouldn't be any binaop and arithmetic value and arithmetic variable.
        if ('+' in s) or ('-' in s) or ('*' in s):
            return False
        s_exp_wout_op = re.split(r'\s+ve\s+|\s+veya\s+', s)
        if '' in s_exp_wout_op:   # evaluates the case in which operators are next to each other.
            return False
        for m in s_exp_wout_op:
            if not ((m in l_term) or (m in l_var_name)):
                return False
        return 'dogru'
    elif ('+' in s) or ('-' in s) or ('*' in s):
        # If there are no binop but there are binaop, there shouldn't be any logic value and logic variable.
        s_exp_wout_op = re.split(r'\s*\+\s*|\s*-\s*|\s*\*\s*', s)
        if '' in s_exp_wout_op:   # evaluates the case in which operators are next to each other.
            return False
        for m in s_exp_wout_op:
            if not ((m in a_term) or (m in a_var_name)):
                return False
        return '1'
    else:
        # If there are no operators in the input string, the values and variables should be either arithmetic or logic.
        if (s in a_term) or (s in a_var_name):
            return '1'
        elif (s in l_term) or (s in l_var_name):
            return 'dogru'
        else:
            return False


file = open("calc.in")
f = file.read()
file.close()
line_list = re.split('\n', f)
for i in range(len(line_list)):    # If the empty lines includes whitespaces, this converts them to ''.
    empty_lines_with_whitespaces = re.findall(r'^\s+$', line_list[i])
    if len(empty_lines_with_whitespaces) != 0:
        line_list[i] = re.sub(empty_lines_with_whitespaces[0], '', line_list[i])
temp = line_list.count('')   # When f is splitted, '' comes from empty lines in f.
for i in range(temp):  # Empty lines are removed.
    line_list.remove('')
for i in range(len(line_list)):
    line_list[i] = line_list[i].strip()

for i in parts:  # parts = ['AnaDegiskenler', 'YeniDegiskenler', 'Sonuc'] are checked.
    if i not in line_list:
        error()
        sys.exit()
indx2 = line_list.index('YeniDegiskenler')
indx3 = line_list.index('Sonuc')

a_var_name = []  # List of arithmetic variables
l_var_name = []  # List of logic variables
for i in range(1, indx2):  # initial statements
    word_list = line_list[i].split()
    var_name = re.findall(r'^([a-zA-Z0-9]{1,10})\s+', line_list[i])
    if len(var_name) == 0:
        error()
        sys.exit()
    if var_name[0] in keywords:
        error()
        sys.exit()
    if var_name[0] not in dict_of_var_name.keys():
        dict_of_var_name[var_name[0]] = dict_of_var_name.get(var_name[0])
    else:    # if the same variable is used more than one.
        error()
        sys.exit()
    # up to here it checks the syntax of variable.
    if word_list[1] != 'degeri' or word_list[-1] != 'olsun':
        error()
        sys.exit()
    val_ch = re.findall(r'degeri\s+(.*)\s+olsun', line_list[i])
    val_ch[0] = val_ch[0].strip()
    temp = val_ch[0].split()
    if len(temp) != 1:
        val_ch[0] = ''
        for t in temp:     # converts strings such as 'iki     nokta   uc' to 'iki nokta uc' to check easily
            val_ch[0] += t + ' '
        val_ch[0] = val_ch[0].strip()
    if (val_ch[0] not in a_term) and (val_ch[0] not in l_term):
        error()
        sys.exit()
    elif val_ch[0] in a_term:
        a_var_name.append(var_name[0])
    elif val_ch[0] in l_term:
        l_var_name.append(var_name[0])

for i in range(indx2+1, indx3):    # mid-statements
    word_list = line_list[i].split()
    var_name = re.findall(r'^([a-zA-Z0-9]{1,10})\s+', line_list[i])
    if len(var_name) == 0:
        error()
        sys.exit()
    if var_name[0] in keywords:
        error()
        sys.exit()
    if var_name[0] not in dict_of_var_name.keys():
        dict_of_var_name.get(var_name[0])
    else:
        error()
        sys.exit()
    if word_list[1] != 'degeri' or word_list[-1] != 'olsun':
        error()
        sys.exit()

    exp = re.findall(r'degeri\s+(.*)\s+olsun', line_list[i])
    exp = exp[0].strip()
    if 'nokta' in exp:
        # to check t_digit_t_digit elements in the string easily, it removes extra spaces before and after 'nokta'.
        exp = re.sub(r'\s*nokta\s*', ' nokta ', exp)
    exp_new = re.sub('ac-parantez', '(', exp)
    exp_new = re.sub('kapa-parantez', ')', exp_new)
    exp_new = re.sub('arti', '+', exp_new)
    exp_new = re.sub('eksi', '-', exp_new)
    exp_new = re.sub('carpi', '*', exp_new)
    exps = exp_new.split()
    if exp == ['']:   # to detect whether there is no expression
        error()
        sys.exit()
    count_ac = 0    # number of '(' in the string
    count_kapa = 0  # number of ')' in the string
    for j in exps:
        if j == '(':
            count_ac += 1
        elif j == ')':
            count_kapa += 1
        if count_kapa > count_ac:  # controls cases that have close parentheses before open parenthesis such as ') ('
            error()
            sys.exit()
    if count_ac != count_kapa:
        error()
        sys.exit()
    if count_ac != 0:
        # If there are parentheses in the string, exp_ctrl() checks syntax of the most inner parentheses
        # and replace them with a shorter string representing the same type(either arithmetic or logic).
        # When all the parentheses are checked, the string will be converted to a string without parentheses.
        while '(' in exp_new:
            temp = []
            empty_parentheses = re.findall(r'\(\s+\)', exp_new)
            if len(empty_parentheses) != 0:
                error()
                sys.exit()
            parentheses = re.findall(r'\(\s+([^()]*[A-Za-z0-9])\s+\)', exp_new)  # list of the most inner parentheses
            for e in parentheses:
                if exp_ctrl(e) is False:
                    error()
                    sys.exit()
                else:
                    temp.append(exp_ctrl(e))  # list of the shorter representation of the most inner parentheses
            # If all the most inner parentheses are the same type of expression(either arithmetic or logic),they are replaced with the shorter ones.
            if (temp == ['1']*len(parentheses)) or (temp == ['dogru']*len(parentheses)):
                exp_new = re.sub(r'\(\s+[^()]*\s+\)', temp[0], exp_new)
            else:  # If not, it is syntax error.
                error()
                sys.exit()
    # The expression does not include parentheses.
    # The expression without parentheses is checked in following code.
    # If it is a logic expression, variable is appended l_var_name.
    # If it is a arithmetic expression, variable is appended a_var_name.
    if exp_ctrl(exp_new) == 'dogru':
        l_var_name.append(var_name[0])
    elif exp_ctrl(exp_new) == '1':
        a_var_name.append(var_name[0])
    else:
        error()
        sys.exit()

for i in range(indx3+1, len(line_list)):   # final-statements check is the same as the expression check in the mid-statements.
    if 'nokta' in line_list[i]:
        line_list[i] = re.sub(r'\s*nokta\s*', ' nokta ', line_list[i])
    exp_new = re.sub('ac-parantez', '(', line_list[i])
    exp_new = re.sub('kapa-parantez', ')', exp_new)
    exp_new = re.sub('arti', '+', exp_new)
    exp_new = re.sub('eksi', '-', exp_new)
    exp_new = re.sub('carpi', '*', exp_new)
    exps = exp_new.split()
    count_ac = 0
    count_kapa = 0
    for j in exps:
        if j == '(':
            count_ac += 1
        elif j == ')':
            count_kapa += 1
        if count_kapa > count_ac:
            error()
            sys.exit()
    if count_ac != count_kapa:
        error()
        sys.exit()
    if count_ac != 0:
        while '(' in exp_new:
            temp = []
            empty_paranthesis = re.findall(r'\(\s+\)', exp_new)
            if len(empty_paranthesis) != 0:
                error()
                sys.exit()
            paranthesis = re.findall(r'\(\s+([^()]*[A-Za-z0-9])\s+\)', exp_new)
            for e in paranthesis:
                if exp_ctrl(e) is False:
                    error()
                    sys.exit()
                else:
                    temp.append(exp_ctrl(e))
            if (temp == ['1'] * len(paranthesis)) or (temp == ['dogru'] * len(paranthesis)):
                exp_new = re.sub(r'\(\s+[^()]*\s+\)', temp[0], exp_new)
            else:
                error()
                sys.exit()
    if exp_ctrl(exp_new) is False:
        error()
        sys.exit()
# If there is no syntax error up to here, following code is executed.
output = open('calc.out', 'w')
output.write("Here Comes the Sun")
output.close()
sys.exit()
```
