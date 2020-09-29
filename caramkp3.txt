/* -----------------------------------------------------------------------------
Program file: caramkp3.c
Author: Keith Caram
Emial: Keith_Caram@student.uml.edu
Date: March 1, 2020
Assignment: program #3 
Course #: INFO 2120.061 
Objective: This program will collect the the first name, last name and middle
			initial of a number of friends along with their full address - 
			containing state and zipcode - as well as their date of birth. The
			state, and birth date will be validated and all data will be checked
			for errors. This will all be stored using structures. Then all of 
			this will then be printed and displayed in a report.
-------------------------------------------------------------------------------- */
#include<stdio.h>
#include<string.h>

	/* Structure definitions */
	/* --------------------- */

struct name
{
	char	last_name [15];
	char	first_name [10];
	char	middle_initial;
};

struct	date
{
	int yyyy;
	int	mm;
	int	dd;
};

struct information
{
	struct name		name;
	char			address [20];
	char			city [15];
	char			state [3];
	long			zip_code;
	struct date		date;
};

struct month
{
	int number_of_days;
};

	/* Function Prototypes */
	/* ------------------- */

void safer_gets (char [], int);
int get_numFriends(void);
int check_the_state(char []);
	
/* Begin Main */
int main(void)
{
	/* Variable Declarations */
	
	struct 	information	friends [10];  	 	
 	struct 	month 		months	[12]  =
	{
		31, 28, 31, 30, 31, 30,
	 	31, 31, 30, 31, 30, 31
 	};
   	 	
	int 	x, num_friends;
	int		notok = 0; /* condition variable for date validation */
	char 	c; 
	
	printf("Welcome to Keith's Friend Tracker TM \n\n");
	
	/* Call function to get number of friends to process */
	num_friends = get_numFriends();
	
	/* Call for loop to get friend info and fill declared 'struct information friends' */
	for (x = 0; x < num_friends; x++)
	{
		printf("\nFriend #%i\n", x + 1);
		
		/* get first name */
		printf("Enter first name: ");
		safer_gets(friends[x].name.first_name, 9);
		
		/* get last name */
		printf("Enter last name: ");
		safer_gets(friends[x].name.last_name, 14);
		
		/* get middle initial */
		printf("Enter middle initial: ");
		scanf("%c", &friends[x].name.middle_initial);
		while ( (c = getchar() != '\n') && c != EOF); /* clear input buffer */
		
		/* get street address*/
		printf("Enter street address: ");
		safer_gets(friends[x].address, 19);
		
		/* get city */
		printf("Enter city: ");
		safer_gets(friends[x].city, 14);
		
		/* do while to get and call function to check state code */
		do
		{	
			/* get state code */		
    		printf ("Enter state: ");
    		safer_gets(friends[x].state, 3);
    			
   			/* convert the state code to uppercase */   
		    friends[x].state[0] = toupper(friends[x].state[0]);
   			friends[x].state[1] = toupper(friends[x].state[1]);
   			
		/* call function to check state code as condition of do while*/   			
		}while (check_the_state(friends[x].state) == 0);/* end do while to check state code if valid*/
		
		/* do while to validate zip code */
		do
		{
			printf("Enter zip code: ");
			scanf("%d", &friends[x].zip_code);
			while ( (c = getchar() != '\n') && c != EOF); /* clear input buffer */
			
			if(friends[x].zip_code < 0 || friends[x].zip_code > 99999)
   				printf("\n***Invalid Zip Code - try again ***\n\n");
			
		}while(friends[x].zip_code < 0 || friends[x].zip_code > 99999);/* end do while to validate zip code */
		
		/* do while to get and validate birth date */
		do
		{
			notok = 0; /* reset do while condition */
			months[1].number_of_days = 28; /* Reset number of days in Feb if you had a leap year birthday in previous loop */
			
			/* get birth date in specified formant*/
			printf("Enter birth date (mm/dd/yyyy): ");
			scanf("%d/%d/%d", &friends[x].date.mm, &friends[x].date.dd, &friends[x].date.yyyy);
			while ( (c = getchar() != '\n') && c != EOF);
			
			/* Validate month */
			if(friends[x].date.mm < 1 ||  friends[x].date.mm > 12)
			{
		   		notok = 1;
		   		puts("Bad Month");
  				continue;
			}
			
			/* Validate Year */
			if(friends[x].date.yyyy < 1900 ||  friends[x].date.yyyy > 5099)
			{
		   		notok = 1;
		   		puts("Bad Year");
  				continue;
			}
			
			/* Validate if it is a leap year and change days in Feb accordingly */
			if(friends[x].date.mm == 2 && friends[x].date.yyyy % 4 == 0 && friends[x].date.yyyy != 2000)
			   months[friends[x].date.mm - 1].number_of_days = 29;
			
			/* Validate number is a valid number for days in each month */	
			if(friends[x].date.dd < 1 ||  friends[x].date.dd > months[friends[x].date.mm - 1].number_of_days)
			{
		   		notok = 1;
		   		puts("Bad Day");
  				continue;
			}
	
		}while(notok == 1);/* end do while */
		
	}/* end for loop */
	
	/* Print sheet for all friend data collected */
	printf("\n\n				List of Keith's Friends\n				-----------------------\n\n");
	printf("Name			    Address		    City      State    Zip	Birthday\n");
	
	/* start for loop to print all data collected into 'struct information friends' */
	for (x = 0; x < num_friends; x++)
	{
		printf("%s %c %-15s", friends[x].name.first_name, friends[x].name.middle_initial, friends[x].name.last_name);
		printf("%20s", friends[x].address);
		printf("%15s", friends[x].city);
		printf("    %3s", friends[x].state);
		printf("    %.5d ", friends[x].zip_code);
		printf("	%.2d/%.2d/%.4d \n", friends[x].date.mm, friends[x].date.dd, friends[x].date.yyyy);
	}/* end print loop */
	
	printf("\n"); /* spacing */
	
	return 0;
}/* end main */


/* List of Functions */

/* Function safer_gets */
void safer_gets (char array[], int max_chars)
{
	int i;/* loop control variable */

	for (i = 0; i < max_chars; i++)
 	{
 		array[i] = getchar();

		if (array[i] == '\n')
        	break;  
	} /* end for */


	if (i == max_chars - 1 )
	
		if (array[i] != '\n')
			while (getchar() != '\n');
			     
	array[i] = '\0';
   
} /* end safer_gets */



/* Function to get number of employees */
int get_numFriends(void)
{
	/* Declare variables. */
	char 	c;
	int 	num_friends;
	
	/* start trap loop to get number of employees */
	
	do
	{
		printf("How many friends do you wish to process? ");
		scanf("%i", &num_friends);
		while ( (c = getchar() != '\n') && c != EOF); /* clear input buffer */
		
		
		if (num_friends < 1 || num_friends > 10) /* trap loop */
			printf("\nEnter a number between 1 and 10 - try again please!\n\n");
			
 	} while (num_friends < 1 || num_friends > 10);/* end do-while */
 	
 	return num_friends;
}/* end get_emplopee */


/* Function to validate state code */
int check_the_state(char stcd[])
{
	int x;/* loop control variable */

	/* Array containing all state codes */	
	char states[51][3] =
	 {"AL",	"AK",	"AZ",	"AR",	"CA",	"CO",	"CT",	"DE",	"DC",	
	  "FL",	"GA",	"HI",	"ID",	"IL",	"IN",	"IA",	"KS",	"KY",	
	  "LA",	"ME",	"MD",	"MA",	"MI",	"MN",	"MS",	"MO",	"MT",	
	  "NE",	"NV",	"NH",	"NJ",	"NM",	"NY",	"NC",	"ND",	"OH",	
	  "OK",	"OR",	"PA",	"RI",	"SC",	"SD",	"TN",	"TX",	"UT",	
	  "VT",	"VA",	"WA",	"WV",	"WI",	"WY"};

	/* start loop to compare user entered state code passed to function
		 to each state code in the state code array */
	for(x = 0; x < 51; x++)
	{
		if (strcmp(stcd, states[x])== 0)
		return 1;
	}/* end for loop */
	
	printf("\nState abbreviation is invalid please re-enter\n\n");
	return 0;

}/* end state code check */
