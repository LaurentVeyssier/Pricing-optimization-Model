# Pricing-optimization-Model
Optimize revenues through pricing algorithm in python - Demand with uniform distribution

# Problem statement
This problem is inspired by a micro challenge proposed at [Kaggle](https://www.kaggle.com/alexisbcook/airline-price-optimization-microchallenge).

The objective is to optimize generated revenues using dynamic pricing by defining a pricing algorithm able to predict and optimize daily prices in response to a changing daily demand. Think about a transportation, hospitality or entertainment industry selling a fixed amount of tickets for a defined event, flight or time-bound service. 

For each event, the pricing function will be run once per day to set that day's ticket price. The seats you don't sell today will be available to sell tomorrow, unless the date of event is reached (for example, the flight leaves that day).

Key problem assumptions:
- Demand is represented by a uniform distribution between 100 and 200. So, for any day in the future, it is equally likely to be each value between 100 and 200 (the demand has equal probabilities to occur).
- You only learn the demand level for each day at the time you need to define price for that day.
- Ticket quantities are capped at the number of seats available.
- Tickets which have not been sold at the date of the event are lost.
- The quantity of tickets you sell on a given day is defined by the simple equation: `tickets sold = demand - price (on that day)`. So you won't sell any tickets if priced at the demand level (walk-away price limit) and you will sell all available tickets if you propose a price equals to the demand less your number of tickets (provided the demand is large enough).
- The sale starts N days before the event with a defined number of tickets to sell. The objective is to maximize revenues generated selling the tickets.

# initial observations
- Since unsold tickets are lost, the algorithm must check and sell all remaining tickets the day before the last. This will be achieved by setting price = demand on day before the last - tickets left. In case the demand is below the number of tickets left, a portion will be lost.
- Revenues = Price x Quantities sold.
  - Quantities sold = demand - price
  - `Revenues = price x (demand - price)`. Revenues are maximized when the function's derivative is 0.
  - dRevenues/dprice = demand - 2 x price so the optimal price maximizing Revenues is `price* = demand / 2`.
  - With above revenues formula, we find back that we do not sell tickets if price = 0 or if price = demand.
-  At optimal price, ticket_sold* = demand - price* = demand - demand/2 = demand/2.
    - This optimal price is only relevant when the number of available tickets is at least half of the demand.
- Let's test different strategies:
  - If we try to sell a lot of tickets when demand is high, the outcome can be sub-optimal. Let's take a demand level of 180, close to the max 200. let's assume we have 30 tickets left. In case we are a couple of days before departure, the probability to get a higher demand level is reduced because it needs to be higher than the current demand and there are very few days (or opportuinities) left. So this makes sense to try selling all remaining tickets.
    - price = demand - tickets left, ie price = 180 - 30 = 150 ===> Revenues = 150 x 30 = 4500.
  - However let's imagine we have many days left before the event. What should we do ? In fact, we could achieve similar revenues by fractioning the sales and create opportunities to outperform:
    - let's sell only 15 tickets when demand is 180. price is improved by 15 at 180 - 15 = 165 ==> Revenues = 165 x 15 = 2475.
    - we need another day with demand of 150 only to equal the first strategy: Let's sell the remaining 15 tickets with a lower demand of 150. price = 150 - 15 = 135 ==> Revenues = 135 x 15 = 2025. Total revenues = 2475 + 2025 = 4500 similar to our first strategy.
  - Conclusion => The logic should not to sell the maximum number of tickets when demand is high but rather sell smaller amounts of tickets at a higher price with high demand and repeat until the last days before the event. This is the optimization objective.
  
  

