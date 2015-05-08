---
layout: default
---

##RadioButton
A RadioGroup contains a group of RadioButton, they should have their own id, so that you can know which is selected.
<pre>
&lt;RadioGroup 
     android:id="@+id/rg"
     android:orientation="horizontal"
     android:layout_gravity="center_horizontal"&gt;
     &lt;RadioButton 
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"
          android:id="@+id/man"
          android:text="男性"
          android:checked="true"/&gt;
     &lt;RadioButton 
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"
          android:id="@+id/woman"
          android:text="女性"/&gt;
&lt;/RadioGroup&gt;
</pre>

-----------
<pre>
radioGroup.setOnCheckedChangeListener(new OnCheckedChangeListener()
{		
		@Override
		public void onCheckedChanged(RadioGroup arg0, int arg1)
		{
			// TODO Auto-generated method stub
			if(arg1==R.id.man)
				show.setText("you are a man!");
			else {
				show.setText("you are a woman!");
			}
		}
	});
</pre>

##CheckBox
<pre>
Button.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener()
{
			
	public void onCheckedChanged(CompoundButton arg0, boolean arg1)
	{
		// TODO Auto-generated method stub
		if(arg1)
			linearLayout.setOrientation(1);
		else {
			linearLayout.setOrientation(0);
		}
	}
});
</pre>

