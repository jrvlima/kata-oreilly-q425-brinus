# **Architectural Katas Q4 2025: AI-Enabled Architecture**

## Session 1 \- Video \- Questions & Answers

### Q1 \- Multi-country, multi-lang support?

**Sam:** Yes, consider being in the EU region, under GDPR.

### Q2 \- Legal/legality compliance?

**Sam:** Yes/No\! O insurance issues, admit everything is ok. For aspects such as: (i) ïf someone steal the vehicle, how do we know(?); (ii) what happens when an e-bike doesn’t get checked out back (?) (iii) if someone is piggybacking for a ride (?). There is a relation between what is legal and/or what is situated into a gray-area. You don’t have to rebuild this system for real, so getting every single bit of detail out of it isn't necessarily required, given you’ve probably only got a few weeks to work on this problem (legality). The only kind of area where sort of legality of enforcement you need to deal with is: (i) people not returning vehicles to the right location; (ii) people stealing things. It is a system to replace an existing system; just assume it’s a complete greenfield rebuild\!

### Q3 \- GEO restrictions? City boundaries etc.

**Sam:** There is in terms of where we need vehicles to be brought back to (location restriction). Things like e-bikes and scooters can’t be left everywhere \- there are certain zones where they’re allowed to be left. And there are also certain zones where mobility scooters and e-bikes might be speed-limited as well, because of certain countries' legal restrictions.

### Q4 \- Can cars and vans be parked in any parking lots?

**Sam:** During the rental: absolutely yes. But when you return it, they have to come back to the designated spots; they have to be returned to parking bays.

### Q5 \- Using support for 3rd-party applications? Google Maps, GPS apps etc.

**Sam:** Yes. There is a GPS API that allows you to know where the vehicle currently is. General part.

### Q6 \- Daily commuters and people can park vehicles on their own premises overnight?

**Sam:** No\! You can only rent these things for up to 12 hours. Pay for the duration of the rental. Attention for dealing with vehicles not returned after the duration of the rental.

### Q7 \- If the designation (designated parking slot?) is a turn spot and is not near the custom premise, and can that cause a logistical change?

**Sam:** Yes\! That’s actually NOT something we’re fully under control of, BTW, the system may help the customer if previous information about the destination is provided before starting the trip.

### Q8 \- How to handle vehicles out of the charge before returning?

**Sam:**  E-bikes and scooters continue working without batteries. In the case of cars or vans, we probably should not allow the booking to go ahead if the car doesn’t have enough charge. When you return a vehicle, you have to plug it onto one of our EV charges, which is in the parking bays. We can have a recovery fleet for worst cases.

### Q9 \- How many total customers and total rentals on average per day?

**Sam:** I haven’t made up a number around that. Assume we are operating in full power right now. Well, in the EU, for each country, assume 5,000 e-bikes, 5,000 scooters,  a couple of 100 cars and 100 vans, I mean 200 cars and 200 vans for each country.

### Q10 \- Do we accept subscriptions?

**Sam:** It is NOT the main focus at the moment. Unless you feel this subscription module will impact our business challenges (positively or negatively) and would change the architecture, it's NOT something we’re overly worried about at the moment. 

### Q11 \- Are we planning to scale up?

**Sam:** Yes\! Definitely we always want more people using and to put more money into the vehicle acquisition. Each vehicle should have a great impact since it could be used several times by several people. We have to use them more.

### Q12 \- Is there a maximum weight carried by scooters or vans?

**Sam:**  No\! The range is on an EV charge.

### Q13 \- Cross-country border support?

**Sam:** For round-trip rentals there is no problem. The next question is related to this ine.

### Q14 \- Different logic for one-way trip with additional fees?

**Sam:** Hmmm… Where to return the vehicle implies logistic issues, but it can improve the business model. Think about having not only fees but also rewards for returning vehicles to the points that are less actioned. 

### Q15 \- High-end users are accomplished?

**Sam:** No, they actually buy things, they do not rent. But it depends on how the solution accomplishes different public \- “equity”. We are targeting people that don’t want to own their own vehicles or are not in a position to bring those vehicles with them.

### Q16 \- Is it possible to extend a car rental in a not programmed-event during the rental?

**Sam:** Yes\! Through the app.

### Q17 \- What is the GUI interface into the vehicles?

**Sam:** I’m gonna let you define what the interface should be\! Another interface integrations with HQQ/Telemetric apps etc is also part of the solution to the architecture design.

