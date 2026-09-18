#include <stdio.h>
#include <string.h>

char stack[100], input[100];
int top = -1, ip = 0;

void shift()
{
    stack[++top] = input[ip++];
    stack[top + 1] = '\0';
}

int reduce()
{
    /* F -> i */
    if (top >= 0 && stack[top] == 'i')
    {
        stack[top] = 'F';
        return 1;
    }

    /* F -> (E) */
    if (top >= 2 &&
        stack[top - 2] == '(' &&
        stack[top - 1] == 'E' &&
        stack[top] == ')')
    {
        top -= 2;
        stack[top] = 'F';
        stack[top + 1] = '\0';
        return 1;
    }

    /* T -> T * F */
    if (top >= 2 &&
        stack[top - 2] == 'T' &&
        stack[top - 1] == '*' &&
        stack[top] == 'F')
    {
        top -= 2;
        stack[top] = 'T';
        stack[top + 1] = '\0';
        return 1;
    }

    /* T -> T / F */
    if (top >= 2 &&
        stack[top - 2] == 'T' &&
        stack[top - 1] == '/' &&
        stack[top] == 'F')
    {
        top -= 2;
        stack[top] = 'T';
        stack[top + 1] = '\0';
        return 1;
    }

    /* T -> F */
    if (top >= 0 && stack[top] == 'F')
    {
        stack[top] = 'T';
        return 1;
    }

    /* E -> E + T */
    if (top >= 2 &&
        stack[top - 2] == 'E' &&
        stack[top - 1] == '+' &&
        stack[top] == 'T')
    {
        top -= 2;
        stack[top] = 'E';
        stack[top + 1] = '\0';
        return 1;
    }

    /* E -> E - T */
    if (top >= 2 &&
        stack[top - 2] == 'E' &&
        stack[top - 1] == '-' &&
        stack[top] == 'T')
    {
        top -= 2;
        stack[top] = 'E';
        stack[top + 1] = '\0';
        return 1;
    }

    /* E -> T */
    if (top >= 0 && stack[top] == 'T')
    {
        stack[top] = 'E';
        return 1;
    }

    return 0;
}

int main()
{
    printf("Enter arithmetic expression (use i for identifier): ");
    scanf("%s", input);

    strcat(input, "$");

    printf("\nStack\tInput\tAction\n");
    printf("--------------------------------\n");

    while (!(stack[0] == 'E' && top == 0 && input[ip] == '$'))
    {
        if (reduce())
        {
            printf("%s\t%s\tReduce\n", stack, input + ip);
        }
        else if (input[ip] != '$')
        {
            shift();
            printf("%s\t%s\tShift\n", stack, input + ip);
        }
        else
        {
            printf("%s\t%s\tError\n", stack, input + ip);
            printf("\nString Rejected!\n");
            return 0;
        }
    }

    printf("%s\t$\tAccept\n", stack);
    printf("\nString Accepted!\n");

    return 0;
}