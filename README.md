# Multi2sim

I have study the branch predictor of the **m2s** simulator, and added the **gshare predictor.** 

## Some theory about gshare

On this predictor we use one essential component, the branch history (**bhr**), which is a "memory" that contain the **n** last predictions. We need to use it to make the next prediction and record the actual prediction, this is done this way:

<center>
	<div>
    	BHR &oplus; PC
	</div>
</center>

Now, we need to do this operation on the less significant bits, so we have to displace the bits on the **bhr**, but how much? well, we need to have the log<sub>2</sub>(bimodal size) minus the bhr size.

## Implementation

Once we know this, we can make this simple approach to the solution:

```c
unsigned int mask = (1 << x86_bpred_gshare_hist) - 1;
uop->gsindex = ( (uop->eip >> 2) ^ (bpred->gshare_bhr >> bpred->gshare_displacement) ) & mask;
uop->gspred = bpred->bimod[uop->gsindex] > 1;
bpred->gshare_bhr = (bpred->gshare_bhr >> 1) | ( (bpred->gshare_bhr) << (31) );
uop->pred = uop->gspred;
```

## References

[McFarling-Combining Branch Predictors](https://www.hpl.hp.com/techreports/Compaq-DEC/WRL-TN-36.pdf)

[Analysis of Combined Bimodal and GShare Branch Prediction Schemes](https://pdfs.semanticscholar.org/ec18/ecaac568ede5f3e50e139ebe340ae877d241.pdf)