#! /bin/bash

if [[ $1 == "test" ]]
then
  PSQL="psql --username=postgres --dbname=worldcuptest -t --no-align -c"
else
  PSQL="psql --username=freecodecamp --dbname=worldcup -t --no-align -c"
fi

# Do not change code above this line. Use the PSQL variable above to query your database.
#echo $($PSQL "TRUNCATE teams, games")
echo $($PSQL "TRUNCATE teams, games RESTART IDENTITY")


cat games.csv | while IFS="," read YEAR ROUND WINNER OPPONENT WINNER_GOALS OPPONENT_GOALS
do
  #insert winners into database
  if [[ $WINNER != "winner" ]]
  then
  #check to see if winner is already in the database
  GET_WINNER=$($PSQL "SELECT name from TEAMS where name='$WINNER'")
  #if GET_WINNER is null, insert the data
  if [[ -z $GET_WINNER ]]
  then
  INSERT_WINNER_NAME=$($PSQL "INSERT into TEAMS(name) VALUES('$WINNER')")
  fi
  fi

  #insert Opponents into database
  if [[ $OPPONENT != "opponent" ]]
  then
  #check to see if opponent is already in the database
  GET_OPPONENT=$($PSQL "Select name from TEAMS where name='$OPPONENT'")
  #if GET_OPPONENT is null, insert data
  if [[ -z $GET_OPPONENT ]]
  then
  INSERT_OPPONENT_NAME=$($PSQL "INSERT into TEAMS(name) VALUES('$OPPONENT')")
  fi
  fi
done



cat games.csv | while IFS="," read YEAR ROUND WINNER OPPONENT WINNER_GOALS OPPONENT_GOALS
do
  if [[ $YEAR != "year" ]]
  then
#get winner_id
WINNER_ID=$($PSQL "SELECT team_id from teams where name='$WINNER'")
#echo "winner is" $WINNER_ID

#get opponent_id
OPPONENT_ID=$($PSQL "SELECT team_id from teams where name='$OPPONENT'")
#echo "opponent is" $OPPONENT_ID

#echo $YEAR, $ROUND, $WINNER_GOALS, $OPPONENT_GOALS, $WINNER_ID, $OPPONENT_ID

#insert data
INSERT_GAMES_DATA=$($PSQL "INSERT into games(year, round, winner_goals, opponent_goals, winner_id, opponent_id) VALUES($YEAR, '$ROUND', $WINNER_GOALS, $OPPONENT_GOALS, $WINNER_ID, $OPPONENT_ID)")
  fi
done
