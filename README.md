package music;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

import javax.sound.midi.*;
import javax.swing.*;
import java.util.*;

public class midimusicplayer {
	JFrame frame;
	JPanel mainpanel;
	Track track;
	Sequence sequence;
	Sequencer sq;
	ArrayList<JCheckBox> checkboxlist;
	String[] instrumentnames={"Bass Drum","Closed Hit-Hat","Open Hi-hat","Acoustic Snare","Crash Cymbal","Hand Clap","High Tom","Hi Bongo","Marcas","Whistle","Low Conga","CowBell","Vibraslap","Low-mid Tom","High Agogo","Open Hi Congo"};
	int[] instruments={35,42,46,38,49,39,50,60,70,72,64,56,58,47,67,63};
	public static void main(String[] a){
		midimusicplayer midi=new midimusicplayer();
		midi.start();
	}
	public void start(){
		frame=new JFrame("beat box");
		BorderLayout layout=new BorderLayout();
		layout.setVgap(20);
		mainpanel=new JPanel(layout);
		checkboxlist = new ArrayList<JCheckBox>();
		mainpanel.setBorder(BorderFactory.createEmptyBorder(10,10,10,10));
		Box eastbox=new Box(BoxLayout.Y_AXIS);
		JButton start=new JButton("Start");
		start.addActionListener(new mystartlistner());
		eastbox.add(start);
		JButton stop=new JButton("Stop");
		stop.addActionListener(new mystoplistner());
		eastbox.add(stop);
		JButton tempoup=new JButton("Tempo up");
		tempoup.addActionListener(new mytempouplistner());
		eastbox.add(tempoup);
		JButton tempodown=new JButton("Tempo down");
		tempodown.addActionListener(new mytempodownlistner());
		eastbox.add(tempodown);
		mainpanel.add(BorderLayout.EAST,eastbox);
		Box westbox=new Box(BoxLayout.Y_AXIS);
		for(int i=0;i<16;i++){
			westbox.add(new JLabel(instrumentnames[i]));
			
		}
		mainpanel.add(BorderLayout.WEST, westbox);
		GridLayout grid=new GridLayout(16,16);
		
		JPanel main=new JPanel(grid);

		for(int i=0;i<256;i++){
			JCheckBox cb=new JCheckBox();
			cb.setSelected(false);
			checkboxlist.add(cb);
			main.add(cb);
		}
		mainpanel.add(BorderLayout.CENTER,main);
		frame.getContentPane().add(mainpanel);
		startmidi();
		
		
		frame.setVisible(true);
		frame.setBounds(50,50,300,300);
		frame.pack();
		frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		
		}
	public void startmidi(){
		
		try {
			sq = MidiSystem.getSequencer();
			sq.open();
			sequence=new Sequence(Sequence.PPQ,4);
			 track=sequence.createTrack();
			sq.setTempoInBPM(120);
		} catch (MidiUnavailableException | InvalidMidiDataException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
		
		}
	public void buildtrack(){
		int[] tracklist=null;
		sequence.deleteTrack(track);
	 track=sequence.createTrack();
		for(int i=0;i<16;i++){
			tracklist =new int[16];
			int key=instruments[i];
			for(int j=0;j<16;j++){
				JCheckBox jb=checkboxlist.get(j+i*16);
				if(jb.isSelected()){
					tracklist[i]=key;
				}
				else
					tracklist[i]=0;
			}
			maketracks(tracklist);
			track.add(makeevents(176,1,127,0,16));
						
		}
		try{
		track.add(makeevents(192,9,1,0,15));
		sq.setSequence(sequence);
		sq.setLoopCount(sq.LOOP_CONTINUOUSLY);
		sq.start();
		sq.setTempoInBPM(120);
		}
		catch(Exception ex){
			ex.printStackTrace();
		}
		
		
		
	}
	public void maketracks(int[] tracklist) {
		
		for(int i=0;i<16;i++){
			if(tracklist[i]!=0){
				try{
				int key=tracklist[i];
				track.add(makeevents(144,9,key,100,i));
				track.add(makeevents(128,9,key,100,i+1));
				}
				catch(Exception ex){
					ex.printStackTrace();
				}
				
			}
		}
	}
	public MidiEvent makeevents(int cmd,int chan,int key,int two,int tick) {
		MidiEvent event=null;
		try{

		ShortMessage a=new ShortMessage();
		a.setMessage(cmd,chan,key,two);
		event =new MidiEvent(a,tick);
		}
		catch(Exception ex){
			ex.printStackTrace();
		}
		return event;
	}
	public class mystartlistner implements ActionListener{

		@Override
		public void actionPerformed(ActionEvent arg0)  {
			try {
				buildtrack();
			} catch (Exception e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			// TODO Auto-generated method stub
			
		}
		
	}
	public class mystoplistner implements ActionListener{

		@Override
		public void actionPerformed(ActionEvent e) {
			sq.stop();
			
		}
		
	}
	public class mytempouplistner implements ActionListener{

		@Override
		public void actionPerformed(ActionEvent e) {
			float tempfactor=sq.getTempoFactor();
			sq.setTempoFactor((float)(tempfactor*1.03));
			
		}
		
	}
	public class mytempodownlistner implements ActionListener{

		@Override
		public void actionPerformed(ActionEvent e) {
			float tempfactor=sq.getTempoFactor();
			sq.setTempoFactor((float)(tempfactor*0.97));
			
		}
		
	}
	
		
	}


