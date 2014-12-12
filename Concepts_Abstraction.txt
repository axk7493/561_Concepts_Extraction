import gov.nih.nlm.nls.metamap.*;
import java.io.FileReader;
import java.io.BufferedReader;
import java.io.PrintWriter;
import java.text.BreakIterator;
import java.util.ArrayList;
import java.util.List;
import java.util.Locale;


public class Concepts_Abstraction 
{
	public static String[] ConceptId= new String[10000000];
	public static String[] ConceptName=new String[10000000];
	public static String SemanticType;
	public static int i=0;
  public static void main(String[] args)

  {
	  
//	  List symTypes = ArrayList<String>();
//	  symTypes.add("dsyn");
//	  symTypes.add("antb");
	  
	  
	  
	 
	  
	  List<String> Options1 = new ArrayList<String>();
	  Options1.add("aapp,dsyn,diap");
	  System.out.println(Options1);
	  MetaMapApi api = new MetaMapApiImpl();
	  api.setOptions("-R MSH");
	  List<String> Options = new ArrayList<String>();
	  Options.add("-J") ;
	  if (Options1.size()>0)
	  {
		 api.setOptions(Options1); 
		 
	  }
	  Options.add("dsyn");
	  
	  try //1st Try
	  {
		  BufferedReader br=new BufferedReader(new FileReader("sample_abstract.txt")); 
		  PrintWriter sentenceWriter = new PrintWriter("Sentence.tsv");
		  String record = null;
		  while((record=br.readLine()) != null){
		  String[] lineparse = record.split("\t");			  
		  
		  String PMID = lineparse[0];
		  String Abstract = lineparse[2];
		 // System.out.println(Abstract);
          int senId = 0;
		  BreakIterator iterator = BreakIterator.getSentenceInstance(Locale.US);
		  iterator.setText(Abstract);
		  int start = iterator.first();
		  for (int end = iterator.next(); end != BreakIterator.DONE;start = end, end = iterator.next())
		  
		  {
		    String Line = Abstract.substring(start,end);
		  
		    
				  //System.out.println("This"+" "+"Sentence"+" "+"\""+ Line+"\""+" "+"is being processed");
				  
				  if (Line != null && !Line.isEmpty() ) //label1
				  {
					 
					  List<Result> resultList=api.processCitationsFromString(Line);
					   
					  Result result = resultList.get(0);
					 
					
					   try  //3rd Try Block
					   {
					
						   for (Utterance utterance: result.getUtteranceList()) 
							  {
							   
							   
							  
							     for (PCM pcm: utterance.getPCMList()) 
							      {

							    	 
							    	 
							    	    for (Mapping map: pcm.getMappingList())
							         	{
							    	  		
							    	  		
							    	  		for (Ev mapEv: pcm.getCandidateList()) 
							    	  	 		{
							    	  			
							    	  			 i=0;
									    	     if (!(mapEv.equals(i)))
									    	      {
									    	    	ConceptId[i]=mapEv.getConceptId();
										        	ConceptName[i]=mapEv.getConceptName();
							    	  				
										        	 System.out.println(mapEv.getConceptName()+"\t"+mapEv.getScore());
										        	 System.out.println("Here is your concept Name result");
							    	  			     //ConceptId= mapEv.getConceptId();
							    	  			     //ConceptName= mapEv.getConceptName();
							    	  			    //SemanticType=mapEv.getSemanticTypes();
							    	  			    
							    	  			   
							    	  				
										        	 i+=1;
									    	      }	
							    	  	 		}
							         	 }
							         
							         }
							  // }
						   
						   sentenceWriter.println(PMID+"\t"+senId+"\t"+Line+"\t"+ConceptId[i]+"\t"+ConceptName[i]);
						   sentenceWriter.flush();
						   System.out.println("#################End of Processing senetence###############");
						   //csvOutput.endRecord();
						   senId ++;
						   
					   }
				  }
				  
					   catch(Exception e)   //catch block for 3rd Try block
					   {
						   e.printStackTrace();
					   }
					   
					   
				  }
				  else   // else for if outside
				  {
					  break; 
				  }
					  
			  }
			   
			  //csvOutput.close();
		 
	  }
		 sentenceWriter.close();
		 br.close();
  }
	  
	  catch(Exception e) //Catch for 1st Try block
	  {
		  e.printStackTrace();
	  }
	  
  }
  
}
