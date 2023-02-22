#!/bin/bash
PSQL="psql -X --username=freecodecamp --dbname=salon --tuples-only -c"

#empty tables to run test data
#echo $($PSQL "TRUNCATE customers, appointments")


echo -e "\n~~Welcome to Current Cuts~~
        \nWhat service can we provide you with today?"

MAIN_MENU(){
  if [[ $1 ]]
  then
    echo -e "\n$1"
  fi

#display a list of services
  SERVICES=$($PSQL "SELECT service_id, name FROM services")
  echo "$SERVICES" | while read SERVICE_ID BAR SERVICE_NAME
  do
  echo "$SERVICE_ID) $SERVICE_NAME"
  done

read SERVICE_ID_SELECTED

case $SERVICE_ID_SELECTED in
  1) CUT_MENU ;;
  2) COLOR_MENU ;;
  3) TRIM_MENU ;;
  4) STYLE_MENU ;;
  5) EXTENSIONS_MENU ;;
  6) FACIAL_MENU ;;
  7) EXIT ;;
  *) MAIN_MENU "I could not find that service. What would you like today?" ;;
esac
}

CUT_MENU(){
#get customer phone number
echo -e "\nWhat is your phone number?"
read CUSTOMER_PHONE

#check for existing customer
CUSTOMER_NAME=$($PSQL "Select name from customers where phone='$CUSTOMER_PHONE'")

#if not a current customer, add them
if [[ -z $CUSTOMER_NAME ]]
then
echo -e "\n I don't have a record of that phone number. \nWhat is your name?"
read CUSTOMER_NAME

INSERT_NEW_CUSTOMER=$($PSQL "INSERT INTO customers(phone, name) VALUES('$CUSTOMER_PHONE', '$CUSTOMER_NAME')")
fi

#get service name customer is signing up for
CUSTOMER_SERVICE_SELECTED=$($PSQL "SELECT name from services where service_id=$SERVICE_ID_SELECTED")

#get customer id
CUST_ID=$($PSQL "SELECT customer_id from customers where phone='$CUSTOMER_PHONE'")

#get requested appointment time
echo -e "\nWhat time would you like your$CUSTOMER_SERVICE_SELECTED, $CUSTOMER_NAME?"
read SERVICE_TIME

#INSERT INTO APPOINTMENT TABLE
SET_APPOINTMENT=$($PSQL "INSERT into appointments(customer_id, service_id, time) VALUES($CUST_ID,$SERVICE_ID_SELECTED,'$SERVICE_TIME')")

echo -e "\nI have put you down for a$CUSTOMER_SERVICE_SELECTED at $SERVICE_TIME, $CUSTOMER_NAME."

}

COLOR_MENU(){
#get customer phone number
echo -e "\nWhat is your phone number?"
read CUSTOMER_PHONE

#check for existing customer
CUSTOMER_NAME=$($PSQL "Select name from customers where phone='$CUSTOMER_PHONE'")

#if not a current customer, add them
if [[ -z $CUSTOMER_NAME ]]
then
echo -e "\n I don't have a record of that phone number. \nWhat is your name?"
read CUSTOMER_NAME

INSERT_NEW_CUSTOMER=$($PSQL "INSERT INTO customers(phone, name) VALUES('$CUSTOMER_PHONE', '$CUSTOMER_NAME')")
fi

#get service name customer is signing up for
CUSTOMER_SERVICE_SELECTED=$($PSQL "SELECT name from services where service_id=$SERVICE_ID_SELECTED")

#get customer id
CUST_ID=$($PSQL "SELECT customer_id from customers where phone='$CUSTOMER_PHONE'")


#get requested appointment time
echo -e "\nWhat time would you like your$CUSTOMER_SERVICE_SELECTED, $CUSTOMER_NAME?"
read SERVICE_TIME

#INSERT INTO APPOINTMENT TABLE
SET_APPOINTMENT=$($PSQL "INSERT into appointments(customer_id, service_id, time) VALUES($CUST_ID,$SERVICE_ID_SELECTED,'$SERVICE_TIME')")

echo -e "\nI have put you down for a$CUSTOMER_SERVICE_SELECTED at $SERVICE_TIME, $CUSTOMER_NAME."

}

TRIM_MENU(){
  echo -e "\nThere are no appointments available for a trim today."
}

STYLE_MENU(){
  echo -e "\nThere are no appointments available for a style today."
}

EXTENSIONS_MENU(){
  echo -e "\nThere are no appointments available for hair extentions today."
}

FACIAL_MENU(){
  echo -e "\nThere are no appointments available for a facial today."
}


EXIT(){
  echo -e "\nThank you for stopping by Current Cuts."
}

MAIN_MENU
